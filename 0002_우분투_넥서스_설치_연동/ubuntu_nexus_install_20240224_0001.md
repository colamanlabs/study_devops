
# ubuntu_nexus_install_20240224_0001.md

# NEXUS 설치 목표

우분투 리눅스에 NEXUS (sonatype nexus repository) 를 설치하자.


## 0. 도서검토
알라딘에 nexus 로 찾았는데 못찾았다.\
실물북, 이북 모두 없다.\
현재(2024.02.24)는 없는 것 같다. 



# 1. 설치파일 준비 


## 1.1 제품검토 및 개발사 파일 다운로드 검토
```
상용버전이 있고, 오픈소스 버전이 있다. 

개발사 : sonatype
제품명 : sonatype-nexus-repository
기능 : repository manager


URL : 
1) 제품소개 
sonatype nexus repository pro
https://www.sonatype.com/products/sonatype-nexus-repository

https://www.sonatype.com/products/pricing
유저당 월 $12, 연간 결제


2) 오픈소스 버전 소개, 다운로드 URL
https://www.sonatype.com/products/sonatype-nexus-repository 
페이지에서 아래 푸터 쪽으로 쭉 내리면
커뮤니티 / Nexus Repository OSS 링크가 있다.


https://www.sonatype.com/products/sonatype-nexus-oss-download

"Sonatype Nexus repository oss" 아래 Download Free 버튼 선택했는데, 다운이 안된다.
무료버전도 개인정보 등록을 해야 하나 보다.

일단, 개발사 홈페이지에서 오픈소스 버전 다운로드는 일단 보류하자.
```


## 1.2 apt 를 통한 설치 가능여부 검토 
apt search 로 nexus 로 검색하니 여러개가 나오는데,\
지금 찾고 있는 nexus 인지는 불분명하다.\
nexus 와 함께 repository 라는 단어가 나와야 하는데 안나온다.

```
root@D3-VM-DEV-00:/home/colaman# apt search nexus
정렬 중... 완료
전체 텍스트 검색... 완료
...
...
root@D3-VM-DEV-00:/home/colaman#

apt 로는 아직 제공되지 않는 것 같다.
apt 설치도 일단 보류하자.
```


## 1.3 설치 파일 확보
```
검색으로 찾은 설치글도, 모두 설치파일을 직접 받아 하고 있다.

[참고 URL 1]
https://khs9628.github.io/2022/01/07/2201_Nexus/
다운로드 URL : https://download.sonatype.com/nexus/3/latest-unix.tar.gz

[참고 URL 2]
https://ko.linux-console.net/?p=3525
원문은 외국어 같은데, 한국어 번역본을 제공한다.
다운로드 URL : https://download.sonatype.com/nexus/3/nexus-3.41.1-01-unix.tar.gz

문서를 보면, 다운로드 주소가 있다.
주소를 어떻게 알았는지는 모르겠다. 
아까 홈페이지에서 못찾았다.

디렉토리 인덱싱은 안걸려 있다.
페이지를 보려면 id, pwd 인증을 요구한다.
그런데, 알려진 주소의 파일 다운로드는 된다.

파일명에서 최신버전 같으니, 최신버전으로 받는다.
https://download.sonatype.com/nexus/3/latest-unix.tar.gz
```




# 2. 설치환경 점검
```
홈페이지에서 넥서스 요구환경 글을 못 찾았다.
몇몇글을 참고하면,
- java 기반이고
    - 웹 기반 UI 이나, 별도의 tomcat 설치는 없다.
        - 넥서스 자체에서 웹서비스 환경을 제공하는 것 같다.
- 독립 계정으로 운영
    - nexus
    - 계정 리소스 설정 필요 
```

## 2.1 Java 환경
Java 8 을 요구한다.\
검색으로 찾은 설치 게시글이 모두 Java 8 로 설치했고,\
소나타입 커뮤니티 글을 보면 Java 8 만 지원한다고 한다.

```
[참고 URL 1]
https://community.sonatype.com/t/run-on-java-11/6582
...

[quote="Dawid Sawa, post:2, topic:6582, full:true, username:dsawa"]
No, unfortunately OpenJDK 8 is the only version that’s currently supported, but we are working on making Nexus Repository work on newer JDKs. No ETA yet.
[/quote]
...

[quote="Rafał Kędziorski, post:7, topic:6582, full:true, username:rafal.kedziorski"]
We have 2023 and Java 21 is out. And Nexus works only with Java 8.

Why need the switch to Java 11/17 so long? Is the technical dept so high?
[/quote]


[참고 URL 2]
https://github.com/sonatype/nexus-public/issues/118

```

## 2.2 NEXUS 설치환경 검토
```
- 별도 계정으로 실행된다면
    - 아마도 기본 설치된 자바(/usr/bin/java, 혹은 /usr/local/bin/java 등 ..)를 호출 할 것이다.
        - usr 디렉토리는 공용 디렉토리 이므로, 같은 이름을 써야 하고,
            - 여러 JRE 를 설치하고 기본 선택할 수는 있지만
                - 변경하면 전체 계정에 영향을 미치게 된다.
- 기본설치 자바가 Java 8 인 별도 VM 을 만들자.
    - 나중에 Java 8 에 의존성을 갖는 것들은 여기서 테스트 하자.
    - 포트포워딩 설정없이 바로 접속 가능하도록
        - 네트워크 어댑터 설정은 "어댑터에 브리지" 로 하고 고정 IP 를 부여한다.
		
- 시스템 기본설정, 원격관리 위한 기본 환경 구성
	- 크롬 설치
	- D2Coding 폰트 설치(공홈 다운로드)
	- Visual Studio Code 설치 (공홈 다운로드,  텍스트 에디터 목적)
	- net-tools 설치(apt, ifconfig 등)
	- openssh-server 설치(apt, 원격 SSH 콘솔 접속용)
	- openjdk-8-jdk(apt)
```


```
### 설치후 점검
colaman@D3-VM-DEV-01:~$

### 우분투 버전 
colaman@D3-VM-DEV-01:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.4 LTS
Release:        22.04
Codename:       jammy
colaman@D3-VM-DEV-01:~$

### Java 버전
colaman@D3-VM-DEV-01:~$ java -version
openjdk version "1.8.0_392"
OpenJDK Runtime Environment (build 1.8.0_392-8u392-ga-1~22.04-b08)
OpenJDK 64-Bit Server VM (build 25.392-b08, mixed mode)
colaman@D3-VM-DEV-01:~$
```




## 2.3 환경구성 1 (계정생성, 운영환경 구성)

### 2.3.1 참고 URL
```
https://confluence.curvc.com/pages/viewpage.action?pageId=20251565
useradd nexus
su - nexus


https://blog.naver.com/ncloud24/222906308566
- CENTOS 기반 설명이나, 넥서스 환경 설정 파일 설명이 있다.


https://khs9628.github.io/2022/01/07/2201_Nexus/
- 우분투 기반 설명 이다.


https://ko.linux-console.net/?p=3525
- 외국어 작성글을 한국어로 번역한 것 같다.


https://velog.io/@cptbluebear/Nexus3-Repository-%EA%B5%AC%EC%B6%95
- 우분투 기반 설명 이다.
- 상세하다.
```



### 2.3.2 계정 설정
```
       -d, --home-dir HOME_DIR
           The new user will be created using HOME_DIR as the value for the user's login directory. The default is to
           append the LOGIN name to BASE_DIR and use that as the login directory name. The directory HOME_DIR does
           not have to exist but will not be created if it is missing.


       -s, --shell SHELL
           The name of the user's login shell. The default is to leave this field blank, which causes the system to
           select the default login shell specified by the SHELL variable in /etc/default/useradd, or an empty string
           by default.

```
```
## 1. 유저홈을 어디 둘 것인가?
공통 유저홈(/home) 아래에 둔다.
- nexus:nexus 

## 2. 설치 위치를 어디로 할 것인가?
/opt/nexus/
/opt/sonatype-work/

## 3. 계정의 리소스 권한은?
colaman@D3-VM-DEV-01:~$ ulimit -a | grep "open files"
open files                          (-n) 1024
colaman@D3-VM-DEV-01:~$

현재 1024 다.

### 3.1 기본 리소스 정책 변경하면
https://deepblue28.tistory.com/entry/ubuntu-open-files-limit-%EB%B3%80%EA%B2%BD%ED%95%98%EA%B8%B0

#### 시스템 전체 설정 가능 값 
#### 충분하다.
system-wide limit on the number of open files for all processes
colaman@D3-VM-DEV-01:~$ cat /proc/sys/fs/file-max
9223372036854775807
colaman@D3-VM-DEV-01:~$

https://joonyon.tistory.com/entry/Linux-5%EB%B6%84%EC%9D%B4%EB%A9%B4-%EA%B0%80%EB%8A%A5-ulimit-%ED%99%95%EC%9D%B8-%EB%B0%8F-%EC%84%A4%EC%A0%95-%EB%B0%A9%EB%B2%95feat-open-files

#### /etc/security/limits.conf 수정 
root@D3-VM-DEV-01:/etc/security# ls -al /etc/security/limits.conf
-rw-r--r-- 1 root root 2199  2월 24 12:06 /etc/security/limits.conf
root@D3-VM-DEV-01:/etc/security# 
root@D3-VM-DEV-01:/etc/security# vi limits.conf
...
root@D3-VM-DEV-01:/etc/security#
root@D3-VM-DEV-01:/etc/security# diff ./limits.conf ./limits.conf.20240224
56,72d55
<
<
< # nofile - max number of open file descriptors
< # 2024.02.24
< colaman               soft    nofile  131072
< colaman               hard    nofile  131072
< nexus         soft    nofile  65536
< nexus         hard    nofile  65536
<
< # nproc - max number of processes
< # 2024.02.24
< colaman         soft    nproc  131072
< colaman         hard    nproc  131072
< nexus           soft    nproc  65536
< nexus           hard    nproc  65536
<
<
74d56
<
root@D3-VM-DEV-01:/etc/security#

reboot 한다. 


## 변경전
colaman@D3-VM-DEV-01:~$ ulimit -a
...
open files                          (-n) 1024
...
max user processes                  (-u) 63639
...
colaman@D3-VM-DEV-01:~$

## 변경후 
colaman@D3-VM-DEV-01:~$ ulimit -a
...
open files                          (-n) 131072
...
POSIX message queues         (bytes, -q) 819200
...
max user processes                  (-u) 131072
...
colaman@D3-VM-DEV-01:~$

colaman 계정에 적용이 되었으니,
nexus 계정에도 적용이 될 것이다.


### 유저 생성
useradd -d /home/nexus -m -s /bin/bash nexus

### 유저 삭제
userdel -r nexus 



## 4. nexus 계정 생성 및 리소스 설정 확인 
root@D3-VM-DEV-01:/home#
root@D3-VM-DEV-01:/home# useradd -d /home/nexus -m -s /bin/bash nexus
root@D3-VM-DEV-01:/home# passwd nexus
새 암호:
새 암호 다시 입력:
passwd: 암호를 성공적으로 업데이트했습니다
root@D3-VM-DEV-01:/home#

nexus@D3-VM-DEV-01:~$ ulimit -a
...
open files                          (-n) 65536
...
max user processes                  (-u) 65536
...
nexus@D3-VM-DEV-01:~$
```


## 2.4 환경구성 2 (넥서스 배포)

### 2.4.1 넥서스 다운로드, 디렉토리 구성 및 배포, 권한설정 
https://velog.io/@cptbluebear/Nexus3-Repository-%EA%B5%AC%EC%B6%95


#### 2.4.1.1 넥서스 다운로드 
wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz

```

### wget 으로 다운로드 

nexus@D3-VM-DEV-01:~/WORKS$
nexus@D3-VM-DEV-01:~/WORKS$ wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz
--2024-02-24 12:42:24--  https://download.sonatype.com/nexus/3/latest-unix.tar.gz
download.sonatype.com (download.sonatype.com) 해석 중... 54.219.226.64, 54.193.38.195
다음으로 연결 중: download.sonatype.com (download.sonatype.com)|54.219.226.64|:443... 연결했습니다.
HTTP 요청을 보냈습니다. 응답 기다리는 중... 302 Moved Temporarily
위치: https://sonatype-download.global.ssl.fastly.net/repository/downloads-prod-group/3/nexus-3.65.0-02-unix.tar.gz [따라감]
--2024-02-24 12:42:24--  https://sonatype-download.global.ssl.fastly.net/repository/downloads-prod-group/3/nexus-3.65.0-02-unix.tar.gz
sonatype-download.global.ssl.fastly.net (sonatype-download.global.ssl.fastly.net) 해석 중... 146.75.49.194
다음으로 연결 중: sonatype-download.global.ssl.fastly.net (sonatype-download.global.ssl.fastly.net)|146.75.49.194|:443... 연결했습니다.
HTTP 요청을 보냈습니다. 응답 기다리는 중... 200 OK
길이: 231860503 (221M) [application/x-gzip]
저장 위치: ‘latest-unix.tar.gz’

latest-unix.tar.gz                        100%[====================================================================================>] 221.12M  7.05MB/s    / 31s

2024-02-24 12:42:56 (7.04 MB/s) - ‘latest-unix.tar.gz’ 저장함 [231860503/231860503]

nexus@D3-VM-DEV-01:~/WORKS$


### 파일 압축 해제
``` 
nexus@D3-VM-DEV-01:~/WORKS$ tar xvf ./latest-unix.tar.gz
...
nexus@D3-VM-DEV-01:~/WORKS$



#### 2.4.1.2 디렉토리 생성 및 배포

```
### 배포 
root@D3-VM-DEV-01:/home/colaman# cd /opt
root@D3-VM-DEV-01:/opt# mv /home/nexus/WORKS/nexus-3.65.0-02/ .
root@D3-VM-DEV-01:/opt# mv /home/nexus/WORKS/sonatype-work/ .
root@D3-VM-DEV-01:/opt#

### 소프트링크 생성 
root@D3-VM-DEV-01:/opt#
root@D3-VM-DEV-01:/opt# ln -s /opt/nexus-3.65.0-02 /opt/nexus
root@D3-VM-DEV-01:/opt#
root@D3-VM-DEV-01:/opt# ls -al
합계 20
drwxr-xr-x  5 root  root  4096  2월 24 12:47 .
drwxr-xr-x 20 root  root  4096  2월 24 11:12 ..
drwxr-xr-x  3 root  root  4096  2월 24 11:28 google
lrwxrwxrwx  1 root  root    20  2월 24 12:47 nexus -> /opt/nexus-3.65.0-02
drwxrwxr-x 10 nexus nexus 4096  2월 24 12:44 nexus-3.65.0-02
drwxrwxr-x  3 nexus nexus 4096  2월 24 12:44 sonatype-work
root@D3-VM-DEV-01:/opt#

### 테스트
nexus@D3-VM-DEV-01:~/WORKS$ cd /opt/nexus
nexus@D3-VM-DEV-01:/opt/nexus$ pwd
/opt/nexus
nexus@D3-VM-DEV-01:/opt/nexus$ ls -al
합계 108
drwxrwxr-x 10 nexus nexus  4096  2월 24 12:44 .
drwxr-xr-x  5 root  root   4096  2월 24 12:47 ..
drwxrwxr-x  2 nexus nexus  4096  2월 24 12:44 .install4j
-rw-r--r--  1 nexus nexus   651  1월 25 14:18 NOTICE.txt
-rw-r--r--  1 nexus nexus 17321  1월 25 14:18 OSS-LICENSE.txt
-rw-r--r--  1 nexus nexus 41955  1월 25 14:18 PRO-LICENSE.txt
drwxrwxr-x  3 nexus nexus  4096  2월 24 12:44 bin
drwxrwxr-x  2 nexus nexus  4096  2월 24 12:44 deploy
drwxrwxr-x  7 nexus nexus  4096  2월 24 12:44 etc
drwxrwxr-x  5 nexus nexus  4096  2월 24 12:44 lib
drwxrwxr-x  2 nexus nexus  4096  2월 24 12:44 public
drwxrwxr-x  3 nexus nexus  4096  2월 24 12:44 replicator
drwxrwxr-x 23 nexus nexus  4096  2월 24 12:44 system
nexus@D3-VM-DEV-01:/opt/nexus$

```


## 2.5 넥서스 설정 파일 구성, 환경구성 

### 2.5.1 /opt/nexus/bin/nexus.rc 수정
/opt/nexus/bin/nexus.rc 파일을 수정한다.\
run_as_user 에 실행유저를 지정한다.

```
### 수정 파일 경로
nexus@D3-VM-DEV-01:/opt/nexus/bin$ ls -al /opt/nexus/bin/nexus.rc
-rw-r--r-- 1 nexus nexus 20  2월 24 12:50 /opt/nexus/bin/nexus.rc
nexus@D3-VM-DEV-01:/opt/nexus/bin$

### nexus.rc 수정 
nexus@D3-VM-DEV-01:/opt/nexus/bin$ diff ./nexus.rc ./nexus.rc.20240224_124900
1c1
< run_as_user="nexus"
---
> #run_as_user=""
\ 파일 끝 개행 문자 없음
nexus@D3-VM-DEV-01:/opt/nexus/bin$


### nexus.rc 파일내용 
### 실행유저 말고는 없다.
nexus@D3-VM-DEV-01:/opt/nexus/bin$ cat /opt/nexus/bin/nexus.rc
run_as_user="nexus"
nexus@D3-VM-DEV-01:/opt/nexus/bin$
```


### 2.5.2 /opt/nexus/bin/nexus.vmoptions 수정

```
### 기본 설정(변경전)
nexus@D3-VM-DEV-01:/opt/nexus/bin$ cat nexus.vmoptions

-Xms2703m
-Xmx2703m
-XX:MaxDirectMemorySize=2703m
-XX:+UnlockDiagnosticVMOptions
-XX:+LogVMOutput
-XX:LogFile=../sonatype-work/nexus3/log/jvm.log
-XX:-OmitStackTraceInFastThrow
-Djava.net.preferIPv4Stack=true
-Dkaraf.home=.
-Dkaraf.base=.
-Dkaraf.etc=etc/karaf
-Djava.util.logging.config.file=etc/karaf/java.util.logging.properties
-Dkaraf.data=../sonatype-work/nexus3
-Dkaraf.log=../sonatype-work/nexus3/log
-Djava.io.tmpdir=../sonatype-work/nexus3/tmp
-Dkaraf.startLocalConsole=false
-Djdk.tls.ephemeralDHKeySize=2048
#
# additional vmoptions needed for Java9+
#
# --add-reads=java.xml=java.logging
# --add-exports=java.base/org.apache.karaf.specs.locator=java.xml,ALL-UNNAMED
# --patch-module java.base=${KARAF_HOME}/lib/endorsed/org.apache.karaf.specs.locator-4.3.9.jar
# --patch-module java.xml=${KARAF_HOME}/lib/endorsed/org.apache.karaf.specs.java.xml-4.3.9.jar
# --add-opens java.base/java.security=ALL-UNNAMED
# --add-opens java.base/java.net=ALL-UNNAMED
# --add-opens java.base/java.lang=ALL-UNNAMED
# --add-opens java.base/java.util=ALL-UNNAMED
# --add-opens java.naming/javax.naming.spi=ALL-UNNAMED
# --add-opens java.rmi/sun.rmi.transport.tcp=ALL-UNNAMED
# --add-exports=java.base/sun.net.www.protocol.http=ALL-UNNAMED
# --add-exports=java.base/sun.net.www.protocol.https=ALL-UNNAMED
# --add-exports=java.base/sun.net.www.protocol.jar=ALL-UNNAMED
# --add-exports=jdk.xml.dom/org.w3c.dom.html=ALL-UNNAMED
# --add-exports=jdk.naming.rmi/com.sun.jndi.url.rmi=ALL-UNNAMED
# --add-exports java.security.sasl/com.sun.security.sasl=ALL-UNNAMED
#
# comment out this vmoption when using Java9+
#
-Djava.endorsed.dirs=lib/endorsed
nexus@D3-VM-DEV-01:/opt/nexus/bin$


### 변경후
일단 기본 옵션으로 실행한다.
```



## 2.6 systemd 서비스로 등록

https://velog.io/@cptbluebear/Nexus3-Repository-%EA%B5%AC%EC%B6%95 참고한다.

### 2.6.1 /etc/systemd/system/nexus.service 파일을 생성한다.
```
### /etc/systemd/system/nexus.service 파일을 생성한다.

root@D3-VM-DEV-01:/opt# cat /etc/systemd/system/nexus.service
[Unit]
Description=nexus service
After=network.target

[Service]
Type=forking
LimitNOFILE=65536 # 해당 옵션은 환경에 맞게 적절히 수정한다.
ExecStart=/opt/nexus/bin/nexus start
ExecStop=/opt/nexus/bin/nexus stop
User=nexus
Restart=on-abort

[Install]
WantedBy=multi-user.target

root@D3-VM-DEV-01:/opt#
```


### 2.6.2 systemd 서비스로 등록하고 확인한다.

```
systemctl daemon-reload
systemctl enable nexus.service
systemctl start nexus.service
systemctl status nexus.service


root@D3-VM-DEV-01:/opt#
root@D3-VM-DEV-01:/opt# systemctl daemon-reload
root@D3-VM-DEV-01:/opt#
root@D3-VM-DEV-01:/opt# systemctl enable nexus.service
Created symlink /etc/systemd/system/multi-user.target.wants/nexus.service → /etc/systemd/system/nexus.service.
root@D3-VM-DEV-01:/opt#
root@D3-VM-DEV-01:/opt# systemctl start nexus.service
root@D3-VM-DEV-01:/opt#
root@D3-VM-DEV-01:/opt# systemctl status nexus.service
● nexus.service - nexus service
     Loaded: loaded (/etc/systemd/system/nexus.service; enabled; vendor preset: enabled)
     Active: active (running) since Sat 2024-02-24 12:58:04 KST; 16s ago
    Process: 1761 ExecStart=/opt/nexus/bin/nexus start (code=exited, status=0/SUCCESS)
   Main PID: 1968 (java)
      Tasks: 46 (limit: 19091)
     Memory: 1.2G
        CPU: 25.634s
     CGroup: /system.slice/nexus.service
             └─1968 /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java -server -Dinstall4j.jvmDir=/usr/lib/jvm/java-8-openjdk-amd64/jre -Dexe4j.moduleName=/opt/nexus/bin/nexus -XX:+UnlockDiagnosticVMOptions -Dinstall4j.launcherId=245 -Din>

 2월 24 12:58:03 D3-VM-DEV-01 systemd[1]: Starting nexus service...
 2월 24 12:58:04 D3-VM-DEV-01 nexus[1761]: Starting nexus
 2월 24 12:58:04 D3-VM-DEV-01 systemd[1]: Started nexus service.
lines 1-14/14 (END)
```