---
title: Airflow 2.3 설치 (w. WSL2)
toc: true
categories:
  - Airflow
tags:
  - airflow
  - install
  - scheduler
  - ubuntu
  - webserver
  - Windows
  - WSL
---

Airflow가 아직 윈도우를 지원하지 않기 때문에 WSL을 이용해서 설치하는 방법을 설치하는 겸 Ubuntu에서 설치하는 방법과 큰 차이는 없기 때문에 두 가지 방법을 비교하면서 설명하겠습니다.


 


### **설치 방법 (Ubuntu 20.04, Windows WSL2)**


**Ubuntu 20.04 기준 설치 방법**



```
mkdir ~/airflow
cd ~/airflow

sudo apt update && sudo apt install python3-pip python3.8-dev
python3 -m venv venv
source venv/bin/activate
pip install apache-airflow
echo "export AIRFLOW_HOME=~/airflow" >> ~/.bashrc
source ~/.bashrc
```

 


**Windows WSL2 (Ubuntu 20.04) 기준 설치 방법**



```
mkdir ~/airflow
cd ~/airflow

sudo apt update && sudo apt install python3-pip python3.8-dev
python3 -m venv venv
source venv/bin/activate
pip install apache-airflow
echo "export AIRFLOW_HOME=/mnt/c/Users/USER/airflow" >> ~/.bashrc
source ~/.bashrc

mkdir $AIRFLOW_HOME
sudo chown -R user:user $AIRFLOW_HOME

# AIRFLOW_HOME 소유자 권한이 변경되지 않을 경우 진행
sudo umount /mnt/c
sudo mount -t drvfs C: /mnt/c -o metadata
sudo chown -R user:user $AIRFLOW_HOME
```

 


 


###  **설정 및 실행**


**데이터베이스 초기화 및 Admin 계정 추가**



```
airflow db init
airflow users create \
--username admin \ 
--firstname Admin \ 
--lastname Admin \
--role Admin \
--email admin@example.com
```

 


**Airflow webserver 및 scheduler 실행**


* Airflow는 web server와 scheduler를 각각 실행시켜줘야하기 때문에 터미널을 2개 띄워서 각각 실행해줍니다.



```
airflow webserver -p 8888
```


```
airflow scheduler
```

 


 


 


 


 


 

