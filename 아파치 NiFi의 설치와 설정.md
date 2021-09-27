# 아파치 NiFi 설치와 설정
- Java JDK 패키지 Version 8 설치
<pre>
<code>
apt-get update
apt-get install openjdk-8-jdk
</code>
</pre>

- 다음 명령을 사용하여 Java JDK 설치 디렉토리를 찾습니다.
<pre>
<code>
update-alternatives --config java
</code>
</pre>

- 필요한 환경 변수 구성을 자동화 하는 파일을 생성
<pre>
<code>
gedit /etc/profile.d/java.sh
</code>
</pre>
- 다음은 java.sh 파일 콘텐츠
<pre>
#/bin/bash
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
</code>
</pre>
- 컴퓨터 재부팅
<pre>
<code>
reboot
</code>
</pre>

# 아파치 나이파이 패키치 다운로드
<pre>
<code>
wget http://ftp.unicamp.br/pub/apache/nifi/1.9.2/nifi-1.13.2-bin.tar.gz
</code>
</pre>
- .tar.gz의 압축을 푼다
<pre>
<code>
tar -xvf nifi-1.13.2-bin.tar.gz
</pre>
</code>


# 아파치 나이파이 서버 설치
<pre>
<code>
cd nifi-1.13.2
sudo bin/nifi.sh install
</code>
</pre>

# 아파치 나이파이 환경설정
- 아래의 파일 편집기를 통해 open.
<pre>
<code>
cd nifi-1.13.2/bin
gedit nifi-env.sh
</code>
</pre>
- 아래내용 추가
<pre>
<code>
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
export CLASS_PATH=$JAVA_HOME/lib:$CLASS_PATH
</code>
</pre>

# 아파치 나이파이 서비스 실행
<pre>
<code>
cd nifi-1.13.2
sudo bin/nifi.sh start
</code>
</pre>

# 아파치 나이파이 실행 확인
- http://localhost:8080/nifi 실행 (port 번호는 nifi-env.sh 파일을 편집하여 설정가능)


