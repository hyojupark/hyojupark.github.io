---
title:  "Flask Flask-Classful을 이용한 REST API 적용"
categories: 
  - Server/Flask
tags:
  - flask
  - Flask-Classful
  - REST API
---

REST API를 적용하기 위해 아래 python package를 설치합니다. (작성일 기준 0.14.2 버전 입니다.)

```bash
(venv) > pip install flask_classful
```

그리고 아래 코드를 작성합니다.

## **소스코드**

```python
# app/__init__.py

from flask import Flask

app = Flask(__name__)
```

```python
# run.py

import os
from typing import NoReturn, Any

from flask import url_for, request

from app import app
from app.controller import base_controller
from app.controller import post_controller


class Server(object):
    def __init__(self, host: str, port: int, debug: bool, use_reloader: bool, threaded: bool):
        self.host = host
        self.port = int(os.environ.get("PORT", port))
        self.debug = debug
        self.use_reloader = use_reloader
        self.threaded = threaded

    @staticmethod
    def register_views() -> NoReturn:
        base_controller.BaseView.register(app, route_base='/')
        post_controller.PostView.register(app, route_base='/post')

    @staticmethod
    def url_for_other_page(page) -> Any:
        args = request.view_args.copy()
        args['page'] = page
        return url_for(request.endpoint, **args)

    def run(self) -> NoReturn:
        self.register_views()

        app.jinja_env.globals['url_for_other_page'] = self.url_for_other_page
        app.run(host=self.host, port=self.port, debug=self.debug,
                use_reloader=self.use_reloader, threaded=self.threaded)

if __name__ == '__main__':
    server = Server(host='127.0.0.1', port=5000, debug=True, use_reloader=False, threaded=True)
    server.run()
```

```python
# app/controller/base_controller.py

from flask import jsonify
from flask_classful import FlaskView


class BaseView(FlaskView):
    def __init__(self):
        pass

    def index(self):
        return jsonify(text='index page', success=200)
```

```python
# app/controller/post_controller.py

from flask import jsonify
from flask_classful import FlaskView
from flask_classful import request


class PostView(FlaskView):
    def __init__(self):
        pass

    def get(self, id: int):
        test_data = {
            1: { 'name': 'Hong Gil-dong' }
        }

        data = test_data[id]

        return jsonify(data=data, success=True)

    def post(self):
        json_data = request.json
        name = json_data.get('form-name')

        print('name: ', name)

        return jsonify(success=True)
```

## **결과화면**
![image](/assets/images/posts/2020-07-12-restapi-using-flask-classful/index.png){: .align-center}
index
{: .text-center}

![image](/assets/images/posts/2020-07-12-restapi-using-flask-classful/post_get.png){: .align-center}
get
{: .text-center}

![image](/assets/images/posts/2020-07-12-restapi-using-flask-classful/post_post.png){: .align-center}

![image](/assets/images/posts/2020-07-12-restapi-using-flask-classful/post_pst2.png){: .align-center}
post
{: .text-center}


## **코드설명**

위 예제는 index, GET, POST만 사용했습니다.

flask_classful 자체에서 get, post를 지원하기 때문에 route method를 잡아주지 않아도 GET, POST 동작으로 사용이 가능합니다.

기존의 Flask 코드와 다른 점은 아래와 같이 route를 잡아준다는 점,

```python
base_controller.BaseView.register(app, route_base='/')
post_controller.PostView.register(app, route_base='/post')
```

그리고 Controller에 FlaskView를 사용한다는 점입니다.

REST API를 사용하면 Resource 관리에 유용하기 때문에 꼭 알아둘 필요가 있습니다.