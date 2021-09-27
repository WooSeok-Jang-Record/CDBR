# pgAdmin 4 설치
- 관계형 데이터베이스에 익숙하지 않다면 pgAdmin 4를 이용해서 PostgreSQL을 관리하는 것이 훨씬 편하다. pgAdmin 4에서는 웹기반 GUI를 통해서 직관적으로 데이터를 살펴보거나 테이블을 생성할 수 있다.
- 먼저 pgAdmin 4 패키지가 있는 저장소를 apt에 등록한 후 pgAdmin 4를 설치한다.
<pre>
<code>
sudo curl https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo apt-key add
sudo sh -c 'echo "deb https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'
sudo apt-get update
sudo apt install pgadmin4-web -y
</code>
</pre>
- pgAdmin 4 웹 서버의 설정을 위해 다음 명령을 실행한다.
<pre>
<code>
sudo /usr/pgadmin4/bin/setup-web.sh
</code>
</pre>
