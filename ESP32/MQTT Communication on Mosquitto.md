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
  - 설치 확인
  <pre>
  <code>
  mosquitto
  </code>
  </pre>
  ![image](https://user-images.githubusercontent.com/91245647/137439452-edbcdf11-dd5f-4c6b-a2bd-ed9d27af3063.png)

  port 번호를 확인 가능하며 Initial Port Number는 1883으로 설정.
