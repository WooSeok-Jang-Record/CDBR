Connect HTU2X Temp & Humid Sensor to ESP32
===
- ESP32 Board Pin Out

![image](https://user-images.githubusercontent.com/91245647/137434956-edfc0fdd-db99-46f6-a393-a453fe47bd34.png)

- HTU2X Sensor Pin Out

![image](https://user-images.githubusercontent.com/91245647/137435259-614767bb-0f66-49e9-b5dd-968984ddc28a.png)
1. 3.3V Power Level
2. I2C Communication
3. SDA Pin Connect to ESP32 21 Pin(SDA) & SCL Pin Connect to ESP32 22 Pin(SCL)

- HTU2X Sensor Run on ESP32 ( Aruino 1.8.116 Sketch IDE )
<pre>
<code>
#include "Adafruit_HTU21DF.h"

// HTU_Sensor Init Validation
  float temperature = 0;
  float humidity = 0;

/* Temperature & Humadity Sensor Init */
Adafruit_HTU21DF htu = Adafruit_HTU21DF();

//////////////////////////////////////////////////////////////////////////////////////////////////////////
// Arduino sketch Setup()
void setup()
{
  Serial.begin(115200);
  Serial.println("HTU21D Seonsor Init");
  if (!htu.begin()) {
    Serial.println("Couldn't find sensor!");
    while (1);
  }
  else
  Serial.println("HTU21D Sensor Connect Success!");
}

//////////////////////////////////////////////////////////////////////////////////////////////////////////
// Arduino sketch loop()
void loop()
{
      temperature = htu.readTemperature();
      humidity = htu.readHumidity();
      print("Temp : ");
      println(temperature);
      print("Humid : ");
      println(humidity);
      delay(1000) // 1 sec

  }
</code>
</pre>
