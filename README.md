# PostgreSQL 드라이버
- 우선 https://jdbc.postgresql.org/download.html 로 가서 PostgreSQL JDBC 4.2 driver, 42.2.19를 내려받는다

# 아파치 에어플로의 설치와 설정
  - 먼저 설치 장소를 환경변수 AIRFLOW_HOME에 설정해 주어야 한다
  <pre>
  <code>
  export AIRFLOW_HOME=/opt/airflow
  </code>
  </pre>
  - 다음은 PostgreSQL, Slack, Celery 패키지와 함께 아파치 에어플로를 설치하는 명령이다.
  <pre>
  <code>
  pip install 'apache-airflow[postgres.slack,celery]'
  </code>
  </pre>
- 제일 먼저 데이터베이스를 초기화해야 한다.
<pre>
<code>
airflow db init
</code>
</pre>
- 에어플로 웹 서버를 시동하는 명령
<pre>
<code>
airflow webserver -p 8083
</code>
</pre>
- 다음으로 일정 주기로 데이터 흐름이 실행되게 하려면 에어플로 스케줄러가 필요하다. 현재 터미널에는 웹 서버가 실행 중이므로, 다른 터미널 창을 열어서 다음을 실행.
<pre>
<code>
airflow scheduler
</code>
</pre>
- 웹서버를 시동한 후 아래의 링크로 접속
<pre>
<code>
http://localhost:8083
</code>
</pre>
# 일래스틱서치의 설치와 설정
- 참고 : https://velog.io/@qnfmtm666/elasticsearch-Elasticsearch-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0-Ubuntu-20.04
- 다음과 같은 사항이 필요합니다. (권장)
  - 듀얼코어 이상의 CPU와 4gb 이상의 램 
 
# Step 1 -Install 
- Ubuntu 에서는 ElasticSearch 컴포넌트가 기본적으로 이용이 불가능합니다. 하지만 apt 명령으로 설치 후 Elastic 의 패키지를 설치한다면 이용 가능합니다.
- ElasticSearch는 너를 사랑해서 Elastic 인증키로 서명이 되어 있습니다.(보안을 위해서라는 뜻) 키를 통해 인증받은 패키지들은 모두 사용가능합니다.
- 설치를 위해 다음을 실행하여 Elastic Public GPG 키를 추가.
<pre>
<code>
$ curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
</pre>
</code>
- sources.list.d 폴더에 Elastic 소스리스트를 추가 APT 가 새로운 소스를 찾아야한다.
<pre>
<code>
$ echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
</pre>
</code>
- package list 업데이트
<pre>
<code>
$ sudo apt update
</code>
</pre>
- 설치
<pre>
<code>
$ sudo apt install elasticsearch
</code>
</pre>

# Step 2 - 설정
- 로컬 머신에서 돌리기 위해서 약간의 수정이 필요
- ElasticSearch 는 elasticsearch.yml 에서 설정을 관리하고 있습니다.
<pre>
<code>
$ sudo gedit /etc/elasticsearch/elasticsearch.yml
</code>
</pre>

<pre>
<code>
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
#
network.host: localhost
. . .
</code>
</pre>

# Step 3 - 실행
- systemctl 명령으로 실행
- 초기 수행에 시간이 걸립니다.
<pre>
<code>
 $ sudo systemctl start elasticsearch
 </pre>
 </code>
 - 구동 확인
 <pre>
 <code>
 $ service elasticsearch status
 </pre>
 </code>
 - 위의 명령을 입력후, active running 단어가 보이면 정상 실행중이라는 뜻. 램을 많이 잡아먹는다.
 - 이제 컴퓨터가 켜질 때마다 자동으로 실행하게 해준다.
 <pre>
 <code>
 $ sudo systemctl enable elasticsearch
 </pre>
 </code>
# Step 4 - 확인
- 별도로 포트 설정을 안했다면 http://localhost:9200 으로 접속하면 json 형식으로 값이 뜬다.


