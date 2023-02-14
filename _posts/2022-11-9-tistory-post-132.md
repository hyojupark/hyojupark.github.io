---
title: Helm으로 설치한 Airflow에 MongoDB provider 추가
toc: true
categories:
  - Airflow
tags:
  - airflow
  - authSource
  - docker
  - helm
  - MongoDB
  - provider
  - RFC 3986
---

Helm으로 설치한 Airflow에 MongoDB connection을 추가하려했는데 MongoDB는 없었습니다. 공식 문서([링크](https://airflow.apache.org/docs/apache-airflow-providers-mongo/stable/index.html))를 찾아보면 pip로 추가하라고 되어있지만 Helm으로 설치한 Airflow에 추가하려면 image를 새로 build해야합니다. 아래는 그 과정과 MongoDB Connection을 추가하는 방법을 설명하겠습니다.

## **Airflow Image Build**

**MongoDB provider**를 추가하기 위해서는 <u>기존 airflow image에 provider를 추가해서 빌드</u>해야합니다. 저는 Harbor를 사용하고 있어서 빌드된 image는 Harbor에 올렸습니다.

```Dockerfile
# Dockerfile
FROM apache/airflow:2.4.1
RUN pip install --no-cache-dir apache-airflow-providers-mongo
```


```bash
$ docker build -t HARBOR_URL/project_name/airflow/airflow:2.4.1-ap1
$ docker push HARBOR_URL/project_name/airflow/airflow:2.4.1-ap1
```

<br>

## **Helm 재배포**

이제 **Helm**에 배포돼있는 airflow values를 수정하겠습니다. 기존 values를 가져와서 아래 images 부분을 추가합니다.

```bash
$ helm get values airflow --namespace airflow > airflow-values.yaml
$ vi airflow-values.yaml
...
images:
  airflow:
    repository: HARBOR_URL/project_name/airflow/airflow
    tag: 2.4.1-ap1
```

이제 **Helm**에 배포된 airflow를 재배포합니다.

```bash
$ helm upgrade airflow apache-airflow/airflow --version 1.7.0 --namespace airflow -f airflow-values.yaml
```

<br>

## **provider 설치 확인 및 Connection 추가**

이제 airflow webserver에 들어가봅니다. `Admin > Providers`에 **apache-airflow-providers-mongo**가 추가된 것을 볼 수 있습니다. 이제 Connection을 추가합니다.

![](/assets/images/posts/2022-11-9-tistory-post-132/img-1.png){: .align-center}

위에서 **Password**와 **Extra** 부분이 중요합니다.

- **Password**
	- 비밀번호를 그냥 입력하면 **RFC 3986 오류**가 발생합니다. 그래서 아래 Python 코드로 변환해서 %로 변환된 문자열로 입력해야합니다.
  - ```python
    import urllib.parse

    print(urllib.parse.quote('user!@#$'))


    # 출력
    user%21%40%23%24
    ```

- **Extra**
	+ 그냥 사용하면 **인증 오류**가 발생하기 때문에 `{"authSource": "admin"}`을 추가해줍니다.

<br>

## Reference

* <https://airflow.apache.org/docs/docker-stack/build.html>
* <https://airflow.apache.org/docs/apache-airflow-providers-mongo/stable/index.html>
