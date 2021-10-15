Mosquitto를 통한 ESP32 to ServerPC MQTT Communication
=
![image](https://user-images.githubusercontent.com/91245647/137438951-7124c0a1-b3cd-4b59-8283-cbece0532832.png)

- MQTT
MQTT는 M2M,IOT를 위한 프로토콜로서, 최소한의 전력과 패킷량으로 통신하는 프로토콜.
IOT와 모바일 어플리케이션 등의 통신에 매우 접합한 프로토콜이다.

Ubuntu 20.04 Mosquitto Install
=
- Mosquitto 설치 명령어
  - 리포지토리 추가 & Upadate
  <pre>
  <code>
  sudo apt-add-repository ppa:mosquitto-dev/mosquitto-ppa
  sudo apt-get update
  </code>
  </pre>
  - Mosquitto 설치
  <pre>
  <code>
  sudo apt-get install mosquitto
  </code>
  </pre>
  - Mosquitto-clients 설치
  <pre>
  <code>
  sudo apt-get install mosquitto-clients
  </code>
  </pre>
  - 설치 확인
  <pre>
  <code>
  mosquitto
  </code>
  </pre>
  ![image](https://user-images.githubusercontent.com/91245647/137439452-edbcdf11-dd5f-4c6b-a2bd-ed9d27af3063.png)

  port 번호를 확인 가능하며 Initial Port Number는 1883으로 설정.
  
- Stopping and Starting
<pre>
<code>
sudo /etc/init.d/mosquitto stop
sudo service mosquitto start
</code>
</pre>

- mosquitto.conf 파일 수정
<pre>
<code>
cd /etc/mosquitto
vi mosquitto.conf
</code>
</pre>
편집 화면 하단에 다음 내용 추가
<pre>
<code>
# Password File Location Setting
password_file /etc/mosquitto/passwd

# Blocking Anonymouse Access
bind_address 0.0.0.0
allow_anonymouse true
</code>
</pre>

Mosquitto Publisher & Broker & Subscriber
=
![image](https://user-images.githubusercontent.com/91245647/137443455-be26a04d-b2f8-4310-9fb4-14657a27163d.png)

1. Mosquitto Broker Start ( Ubuntu PC)
<pre>
<code>
mosquitto
</code>
</pre>
2. ESP32 Board Connect ( Develope PC )

![image](https://user-images.githubusercontent.com/91245647/137443774-dcb7b5c7-4b41-4020-b7db-760e839e0540.png)

3. ESP32 Board Connect 확인 ( Ubuntu PC)

![image](https://user-images.githubusercontent.com/91245647/137444304-22eeb302-e6c6-4cb4-a72c-6daada6c2fd9.png)

4. Mosquitto Client 개설
ESP32 Dev 보드에서 Mosquitto Broker에 토픽을 Publish 하고 있다.
따라서, Ubuntu PC 에서 Client를 열어 토픽을 수신한다.

<pre>
<code>
mosquitto_sub -h localhost -t esp_to_bro
</code>
</pre>

-h 는 Broker의 IP를 기입. 
-t 는 ESP32 소스코드에서 지정한 토픽명을 기입.

![image](https://user-images.githubusercontent.com/91245647/137444696-b53079ff-f0ed-4e80-9ad8-1d1f18fb9d8a.png)



