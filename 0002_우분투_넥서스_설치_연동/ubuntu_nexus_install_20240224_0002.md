
# ubuntu_nexus_install_20240224_0002.md

# 목표

설치한 NEXUS 를 연동하자.


# 참고
```
https://devocean.sk.com/blog/techBoardDetail.do?ID=163425

https://waspro.tistory.com/510

https://sound10000w.tistory.com/181

http://yongbi.net/tag/nexus#entry_856

[내부망에서 Nexus Repository 구축하기]
https://velog.io/@kjh48001/%EB%82%B4%EB%B6%80%EB%A7%9D%EC%97%90%EC%84%9C-Nexus-Repository-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0

[내부망,폐쇄망 Maven Repository 설정 및 사용하기]
https://giles.tistory.com/21

https://mine-it-record.tistory.com/160

https://lovelytney.tistory.com/14#google_vignette

[settings.xml 작성방법]
https://web-obj.tistory.com/527

```


# 1. 넥서스 확인
```
1. 최상단 톱니 클릭

2. 좌측 메뉴에서
Repository > Repositories 클릭

3. 우측 페이지에서 
maven-central 클릭

4. Settings 에서
URL 을 복사
http://192.168.1.181:8081/repository/maven-central/

URL 은 내부망 PC 에서 사용할 URL 

URL 을 복사한다.

Remote storage 는 실제 메이븐 리포지토리 저장소
```



# 2. pom.xml 수정한다.
pom.xml 에 추가한다.
```
		<repository>
			<id>central</id>
			<name>Local Nexus Repository</name>
			<url>http://192.168.1.181:8081/repository/maven-central/</url>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
			<releases>
				<enabled>true</enabled>
			</releases>
		</repository>
	</repositories>

	<pluginRepositories>
		<pluginRepository>
			<id>central</id>
			<name>Local Nexus Repository</name>
			<url>http://192.168.1.181:8081/repository/maven-central/</url>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
			<releases>
				<enabled>true</enabled>
			</releases>
		</pluginRepository>
	</pluginRepositories>
```	


# 3. 로컬 저장소 경로를 새로 만든다.
``` 
C:\WORKS\WORKS_STS4_GITHUB\STS4\sts-4.21.0.RELEASE 아래에

\maven\repository 빈 폴더를 만든다.

C:\WORKS\WORKS_STS4_GITHUB\STS4\sts-4.21.0.RELEASE\maven\repository
```


# 4. STS에 settings.xml 을 만들고 등록시킨다.


## 4.1 settings.xml 파일 생성
```
### C:\WORKS\WORKS_STS4_GITHUB\STS4\sts-4.21.0.RELEASE\settings.xml 파일 생성


<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd"> 
    <localRepository>C:\WORKS\WORKS_STS4_GITHUB\STS4\sts-4.21.0.RELEASE\maven\repository</localRepository> 
    <interactiveMode>true</interactiveMode> 
    <offline>false</offline> 
</settings>
```

## 4.2 STS 에 등록

```

1. 메뉴바 > Window > Preferences 선택

2. 좌측 메뉴에서 Maven > User Settings 선택

3. User Settings 의 중간 "User Settings" 에 생성한 settings.xml 을 지정 

```


# 5. 빌드 테스트

```
1. 프로젝트 명 우클릭 하고, run as > maven clean

2. pom.xml 우클릭하고, 업데이트 프로젝트 선택 

3. 로컬 리포지토리에 파일이 쌓이는지 확인한다.
ex) C:\WORKS\WORKS_STS4_GITHUB\STS4\sts-4.21.0.RELEASE\maven\repository

4. 아래 Progress 패널에 지나가는 URL 을 브라우져로 확인해 본다.

ex) http://192.168.1.181:8081/repository/maven-central/org/springframework/spring-tx/5.3.31/spring-tx-5.3.31.jar


``` 



# -- 끝 --










