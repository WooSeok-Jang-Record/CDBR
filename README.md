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
- curl을 이용해서 일래스틱 서치 압축 파일을 내려받는다.
<pre>
<code>
curl https://artifacts.elastic.co/downloads/elasticsearch/\elasticsearch-7.6.0-darwin-x86_64.tar.gz --output elasticsearch.tar.gz
</code>
</pre>
- 압축 파일을 해제한다.
<pre>
<code>
tar xvzf elasticsearch.tar.gz
</code>
</pre>
- 새로 만들어진 디렉터리의 config/elasticsearch.yml을 열고 노트와 클러스터 이름을 설정한다.
<pre>
<code>
cluster.name: custum
node.name: custum
</code>
</pre>
