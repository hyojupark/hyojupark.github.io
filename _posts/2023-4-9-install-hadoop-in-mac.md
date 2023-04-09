---
title: MAC에 Hadoop 설치하기
categories:
  - Hadoop
tags:
  - Hadoop
published: false
---



### Java 설치
```bash
brew install cask
brew install --cask adoptopenjdk/openjdk/adoptopenjdk11
```

### JAVA HOME 위치 확인
```bash
/usr/libexec/java_home
/Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home
```

<!--
vi ~/.bashrc
export JAVA_HOME=/Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home
-->

### Hadoop 설치
```bash
brew install hadoop  # hadoop 3.3.4
```

### 설치 경로 확인
```bash
brew info hadoop
```

<!--
### 설정
vi ~/.bashrc
—
export HADOOP_HOME=/usr/local/Cellar/hadoop/3.3.4
export HADOOP_COMMON_HOME=$HADOOP_HOME
export PATH=$PATH:$HADOOP_HOME/bin
-->


이후 순서
1. 환경변수 설정 (hadoop-env.sh)
2. 설정 추가 (core-site.xml, hdfs-site.xml, mapred-site.xml, yarn-site.xml)
3. 인증서 기반 인증을 통한 password 인증 삭제 (선택)
4. Format NameNode


### 설정 수정
```bash
cd /usr/local/Cellar/hadoop/3.3.4  # HADOOP HOME
cd libexec/etc/hadoop
```

#### 1) hadoop-env.sh
```bash
vi hadoop-env.sh
...
export HADOOP_OPTS="-Djava.net.preferIPv4Stack=true -Djava.security.krb5.realm= -Djava.security.krb5.kdc="
export JAVA_HOME="/Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home"
```

#### 2) core-site.xml
```xml
<configuration>
      <!-- <property>
          <name>hadoop.tmp.dir</name>
          <value>/usr/local/Cellar/hadoop/hdfs/tmp</value>
          <description>A base for other temporary directories.</description>
      </property> -->
      <property>
          <name>fs.default.name</name>
          <value>hdfs://localhost:9000</value>
      </property>
</configuration>
```

#### 3) hdfs-site.xml
```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

#### 4) mapred-site.xml
```xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
  <property>
    <name>mapreduce.application.classpath</name>   
	  <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
  </property>
</configuration>
```

#### 5) yarn-site.xml
```xml
<configuration>
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
	<property>
		<name>yarn.nodemanager.env-whitelist</name>
		<value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
	</property>
</configuration>
```

### 인증서 생성
```bash
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys
```

### NameNode Format
```bash
hdfs namenode -format
```

### Run Hadoop
```bash
cd /usr/local/Cellar/hadoop/3.3.4
libexec/sbin/start-all.sh
jps
```


# Trouble Shooting
1. localhost: ssh: connect to host localhost port 22: Connection refused
  - 설정 -> 공유 -> 원격 로그인 -> 켬


## Reference
  - <https://towardsdatascience.com/installing-hadoop-on-a-mac-ec01c67b003c>
