---
title: intel MAC에 Hadoop 설치하기
categories:
  - Hadoop
tags:
  - Hadoop
# published: false
---

**Hadoop**을 설치하는 과정은 크게 `1) Java 설치`와 `2) Hadoop 설치`로 나뉩니다.

## Java

### Java 11 설치
```bash
brew install cask
brew install --cask adoptopenjdk/openjdk/adoptopenjdk11
```

### JAVA_HOME 확인
```bash
/usr/libexec/java_home
/Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home
```

<br>

## Hadoop

> ### 순서
> 1. Hadoop 설치 및 경로 확인
> 2. 환경변수 및 설정 추가
>   - hadoop-env.sh, core-site.xml, hdfs-site.xml, mapred-site.xml, yarn-site.xml
> 3. NameNode, DataNode Directory 생성
> 4. 인증서 생성 (Password 인증 생략)
> 5. 원격 로그인 허용 (선택)
> 6. Format NameNode
> 7. Hadoop 실행
> 8. 웹서버 접속 및 상태 확인

### 1. Hadoop 설치 및 경로 확인
```bash
brew install hadoop  # hadoop 3.3.4
...

brew info hadoop
```

### 2. 설정 수정
```bash
cd /usr/local/Cellar/hadoop/3.3.4  # HADOOP HOME
cd libexec/etc/hadoop  # Hadoop 설정파일들 위치
```

> #### 1) hadoop-env.sh
> ```bash
> vi hadoop-env.sh
> ...
> export HADOOP_OPTS="-Djava.net.preferIPv4Stack=true -Djava.security.krb5.realm= -Djava.security.krb5.kdc="
> export JAVA_HOME="/Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home"
> ```

> #### 2) core-site.xml
> ```xml
> <configuration>
>       <property>
>           <name>fs.default.name</name>
>           <value>hdfs://localhost:9000</value>
>       </property>
> </configuration>
> ```

> #### 3) hdfs-site.xml
> ```xml
> <configuration>
>     <property>
>         <name>dfs.namenode.name.dir</name>
>         <value>/Users/jangboyun/hadoop/hadoop-3.3.4/dfs/name</value>
>     </property>
>     <property>
>         <name>dfs.datanode.data.dir</name>
>         <value>/Users/jangboyun/hadoop/hadoop-3.3.4/dfs/data</value>
>     </property>
>     <property>
>         <name>dfs.replication</name>
>         <value>1</value>
>     </property>
> </configuration>
> ```

> #### 4) mapred-site.xml
> ```xml
> <configuration>
>   <property>
>     <name>mapreduce.framework.name</name>
>     <value>yarn</value>
>   </property>
>   <property>
>     <name>mapreduce.application.classpath</name>   
> 	  <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
>   </property>
> </configuration>
> ```

> #### 5) yarn-site.xml
> ```xml
> <configuration>
> 	<property>
> 		<name>yarn.nodemanager.aux-services</name>
> 		<value>mapreduce_shuffle</value>
> 	</property>
> 	<property>
> 		<name>yarn.nodemanager.env-whitelist</name>
> 		<value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
> 	</property>
> </configuration>
> ```

### 3. 인증서 생성
```bash
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys
```

### 4. 원격 로그인 허용 (선택)
Mac에서는 Hadoop을 그냥 실행하면 **ssh 관련 오류**(`localhost: ssh: connect to host localhost port 22: Connection refused`)가 발생하기 때문에 추가 설정이 필요합니다.
  - 설정 -> 공유 -> 원격 로그인 -> 켬

### 5. NameNode, DataNode Directory 생성
```bash
mkdir -p /usr/local/Cellar/hadoop/hdfs/namenode
mkdir -p /usr/local/Cellar/hadoop/hdfs/datanode
```

### 6. NameNode Format
```bash
hdfs namenode -format
```

### 7. Hadoop 실행
```bash
cd /usr/local/Cellar/hadoop/3.3.4
libexec/sbin/start-all.sh

jps
76689 SecondaryNameNode
76883 ResourceManager
77058 Jps
76549 DataNode
76984 NodeManager
76445 NameNode
```

**jps**에서 볼 수 있는 데몬은 크게 **HDFS 데몬**과 **Yarn 데몬**으로 나뉩니다. **HDFS 데몬**은 `NameNode`, `SecondaryNameNode`, `DataNode`이고 **Yarn 데몬**은 `ResourceManager`, `NodeManager`입니다.


### 8. 웹서버 접속 및 상태 확인
마지막으로 웹서버에 접속해서 각 구성요소의 상태를 확인할 수 있습니다.

1. **Cluster**
  - http://localhost:8088
2. **HDFS**
  - http://localhost:9870
3. **SecondaryNameNode**
  - http://localhost:9868

<br>

## Reference
  - <https://towardsdatascience.com/installing-hadoop-on-a-mac-ec01c67b003c>
