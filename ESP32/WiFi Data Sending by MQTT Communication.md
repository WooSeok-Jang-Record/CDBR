ESP32 dev 보드의 MQTT 통신 
===
- Header File 추가 (코드에 작성된 해당 헤더파일 라이브러리를 추가해야 함.)
- 참고 [https://velog.io/@adguy/%EC%9A%B0%EB%B6%84%ED%88%ACmqtt-%ED%8A%B8%EB%9F%AC%EB%B8%94-%EC%8A%9B%ED%8C%85-mqttBroker%EC%9D%98-%EB%AA%A8%EB%93%A0%EA%B2%83]
<pre>
<code>
// Yonsei-University-DigitalBioMarker-Research-Lab
// 2021-10-07
// by : WooSeok-Jang
// DFescription : This is a test code for pipeline verification of IoT sensor data.
// Temperature & Humadity Sensor
// Connect Vin to 3-5VDC
// Connect GND to ground
// Connect SCL to I2C Clock pin (GPIO 22)
// Connect SDA tp I2C data pin (GPIO 21)


#include <WiFi.h>
#include <Wire.h>
#include <PubSubClient.h>
#include <ArduinoJson.h>
#include <sstream>
#include "AzureIotHub.h"
#include "Esp32MQTTClient.h"
#include "Adafruit_HTU21DF.h"
#include "time.h"

#define INTERVAL 10000      // Message transmission period conrtol
#define DEVICE_ID "Esp32Device_01" // Temporary Device name.
#define MESSAGE_MAX_LEN 256
#define TIME_MSG_LEN  11   // time sync to PC is HEADER and unix time_t as ten ascii digits
#define TIME_HEADER  255   // Header tag for serial time sync message

// Please input the SSID and password of WiFi
const char* ssid     = "CBDS 2.4";      // 504 WiFi Name
const char* password = "0337420929";    // 504 Wifi Password

// MQTT Init Validation
unsigned long prevUpdateTime = 0L;
const char* mqttServer = "192.168.0.197";
const int mqttPort = 1883;
const char* mqttUser = "user";
const char* mqttPassword = "1234";
const char* nodeName = "ESP32_Temp_01";
const char* topic_pub = "esp_to_bro";   // 메세지를 전송할 토픽의 이름
const char* topic_sub = "bro_to_esp";  // 센서장치로 들어오는 메시지 수신을 위해 새로 만든 토픽.
WiFiClient espClient;
PubSubClient mqttClient(espClient);
char sensor_data_msg[100];

/*String containing Hostname, Device Id & Device Key in the format:                         */
/*  "HostName=<host_name>;DeviceId=<device_id>;SharedAccessKey=<device_key>"                */
/*  "HostName=<host_name>;DeviceId=<device_id>;SharedAccessSignature=<device_sas_token>"    */
static const char* connectionString = "";

const char *messageData = "{\"deviceId\":\"%s\", \"messageId\":%d, \"Temperature\":%f, \"Humidity\":%f}";

int messageCount = 1;
static bool hasWifi = false;
static bool messageSending = true;
static uint64_t send_interval_ms;

// NTP Connect Init Validation
const char* ntpServer = "pool.ntp.org";
const long gmtOffset_sec = 32400;
const int daylightOffset_sec = 0;
struct tm timeinfo;

// HTU_Sensor Init Validation
float temperature = 0;
float humidity = 0;
float datacount = 1;


// JSON Data Init Validation
//StaticJsonDocument<200> doc;
//doc["Device"] = "ESP32_01";
//doc["time"] = 1351824120;


//////////////////////////////////////////////////////////////////////////////////////////////////////////
// Utilities & Functions
static void InitWifi()
{
  Serial.println("Connecting...");
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  hasWifi = true;
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result)
{
  if (result == IOTHUB_CLIENT_CONFIRMATION_OK)
  {
    Serial.println("Send Confirmation Callback finished.");
  }
}

static void MessageCallback(const char* payLoad, int size)
{
  Serial.println("Message callback:");
  Serial.println(payLoad);
}

static void DeviceTwinCallback(DEVICE_TWIN_UPDATE_STATE updateState, const unsigned char *payLoad, int size)
{
  char *temp = (char *)malloc(size + 1);
  if (temp == NULL)
  {
    return;
  }
  memcpy(temp, payLoad, size);
  temp[size] = '\0';
  // Display Twin message.
  Serial.println(temp);
  free(temp);
}

static int  DeviceMethodCallback(const char *methodName, const unsigned char *payload, int size, unsigned char **response, int *response_size)
{
  LogInfo("Try to invoke method %s", methodName);
  const char *responseMessage = "\"Successfully invoke device method\"";
  int result = 200;

  if (strcmp(methodName, "start") == 0)
  {
    LogInfo("Start sending temperature and humidity data");
    messageSending = true;
  }
  else if (strcmp(methodName, "stop") == 0)
  {
    LogInfo("Stop sending temperature and humidity data");
    messageSending = false;
  }
  else
  {
    LogInfo("No method %s found", methodName);
    responseMessage = "\"No method found\"";
    result = 404;
  }

  *response_size = strlen(responseMessage) + 1;
  *response = (unsigned char *)strdup(responseMessage);

  return result;
}

/* Temperature & Humadity Sensor Init */
Adafruit_HTU21DF htu = Adafruit_HTU21DF();

void printLocalTime() {
    if(!getLocalTime(&timeinfo)) {
        Serial.println("Failed to obtain time");
        return;
    }
    Serial.println(&timeinfo, "%A, %B %d %Y %H:%M:%S");
}

void mqttcallback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Message arrived in topic: ");
  Serial.println(topic_sub);
 
  Serial.print("Message: ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
 
  Serial.println();
  Serial.println("-----------------------");
}

float Sensor_Data_To_Json(float count, float temp, float humi){
    StaticJsonDocument<200> Sensor_Data;
      Sensor_Data["Device"] = "ESP32_01";
      Sensor_Data["Data_Count"] = count;
      Sensor_Data["Temp"] = temp;
      Sensor_Data["Humi"] = humi;
    serializeJson(Sensor_Data,Serial);
    Serial.println();
    serializeJson(Sensor_Data,sensor_data_msg);
}



//////////////////////////////////////////////////////////////////////////////////////////////////////////
// Arduino sketch Setup()
void setup()
{
  Serial.begin(115200);
  Serial.println("ESP32 Device");
  Serial.println("Initializing...");

  // Initialize the WiFi module
  Serial.println(" > WiFi");
  hasWifi = false;
  InitWifi();
  if (!hasWifi)
  {
    return;
  }


  Serial.println(" > IoT Hub");
  Esp32MQTTClient_SetOption(OPTION_MINI_SOLUTION_NAME, "GetStarted");
  Esp32MQTTClient_Init((const uint8_t*)connectionString, true);

  Esp32MQTTClient_SetSendConfirmationCallback(SendConfirmationCallback);
  Esp32MQTTClient_SetMessageCallback(MessageCallback);
  Esp32MQTTClient_SetDeviceTwinCallback(DeviceTwinCallback);
  Esp32MQTTClient_SetDeviceMethodCallback(DeviceMethodCallback);

  send_interval_ms = millis();

  /*MQTT Setup*/
  mqttClient.setServer(mqttServer, mqttPort);
  mqttClient.setCallback(mqttcallback);
 
  while (!mqttClient.connected()) {
    Serial.println("Connecting to MQTT...");
    
    if (mqttClient.connect("ESP32Client")) {
      Serial.println("connected");
    } else {
      Serial.print("failed with state ");
      Serial.println(mqttClient.state());
      delay(2000);
    }
  }
  mqttClient.subscribe(topic_sub);
  mqttClient.publish(topic_pub, "ESP32 logged in");
  prevUpdateTime = millis();


  
  Serial.println("HTU21D Seonsor Init");
  if (!htu.begin()) {
    Serial.println("Couldn't find sensor!");
    while (1);
  }
  else
  Serial.println("HTU21D Sensor Connect Success!");

  // init and get the time
    configTime(gmtOffset_sec, daylightOffset_sec, ntpServer);
    printLocalTime();

}



//////////////////////////////////////////////////////////////////////////////////////////////////////////
// Arduino sketch loop()
void loop()
{
  if (hasWifi)
  {
    if (messageSending && 
        (int)(millis() - send_interval_ms) >= INTERVAL)
    {
      // Send teperature data
      char messagePayload[MESSAGE_MAX_LEN];
      temperature = htu.readTemperature();
      humidity = htu.readHumidity();
      snprintf(messagePayload, MESSAGE_MAX_LEN, messageData, DEVICE_ID, messageCount++, temperature, humidity); // Message Sending Key Sentence
      //Serial.println(messagePayload);
      EVENT_INSTANCE* message = Esp32MQTTClient_Event_Generate(messagePayload, MESSAGE);
      Esp32MQTTClient_Event_AddProp(message, "temperatureAlert", "true");
      Esp32MQTTClient_SendEventInstance(message);
      send_interval_ms = millis();
      
      Sensor_Data_To_Json(datacount,temperature,humidity);
      datacount++;
      
      mqttClient.publish(topic_pub, sensor_data_msg);
     
    }
    else
    {
      Esp32MQTTClient_Check();
    }
  }


  delay(10);
}
</code>
</pre>

## 디버그 내용 정리
### MQTT Connect Fail
- 아래의 과정을 통해 문제 해결.
  1. mosquitto.conf 파일 초기화 설정 문제
  해당 경로에 들어간다.
  <pre><code> cd /etc/mosquitto/</code></pre>
  2. 편집기를 통해 아래의 내용 추가
  <pre><code>sudo vi mosquitto.conf</code></pre>
  </br>
  <pre><code>listener 1883 0.0.0.0
   allow_anonymous true</code></pre>
  3. 수정한 conf 파일 적용
  <pre><code>mosquitto -c /etc/mosquitto/mosquitto.conf</code></pre>
  4. 만약 unable to open log file 에러가 발생한다면 아래의 위치로 이동
  <pre><code>cd /var/log/mosquitto</code></pre>
  5. log 파일 권한 변경
  <pre><code>sudo chmod a=rw mosquitto.log</code></pre>
  참조 : [https://recipes4dev.tistory.com/175]
   
  
