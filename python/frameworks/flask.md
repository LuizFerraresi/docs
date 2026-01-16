
# [Flask]()

## Install

```bash
virtualenv .venv
source .venv/bin/activate
```

```bash
pip install flask uwsgi
```

## uWSGI


```ini
# uwsgi.ini
[uwsgi]

http = localhost:8000        # Set http endpoint

wsgi-file = src/app.py       # Set default WSGI callable file
callable = app               # Set default WSGI callable name
need-app = true              # Exit if no app can be loaded

master = true                # Enable master process
enable-threads = true        # Enable threads
lazy-apps = true             # Load apps in each worker instead of the master
workers = 3                  # Spawn the specified number of workers/processes
threads = 2                  # Run each worker in prethreaded mode with the specified number of threads
min-worker-lifetime = 60     # Number of seconds worker must run before being reloaded (default is 60)

single-interpreter = true    # Do not use multiple interpreters (where available)
buffer-size = 4000           # Set internal buffer size, request size
memory-report = true         # Enable memory report
```

All the config options for [uwsgi.ini](https://uwsgi-docs.readthedocs.io/en/latest/Options.html)

> [!WARNING]
> `buffer-size`: Set the max size of a request (request-body excluded), this generally maps to the size of request headers. By default it is 4k. If you receive a bigger request (for example with big cookies or query string) you may need to increase it. It is a security measure too, so adapt to your app needs instead of maxing it out.

```bash
uwsgi --ini uwsgi.ini
```

- [ ]  **no-threads-wait**
- [ ]  **procname**   # set process names
- [ ]  **procname-master # set master process name**
- [ ]  **reload-mercy # set the maximum time (in seconds) we wait for workers and other processes to die during reload/shutdown**
- [ ]  **worker-reload-mercy # set the maximum time (in seconds) a worker can take to reload/shutdown (default is 60)**
- [ ]  **max-requests # reload workers after the specified amount of managed requests**
- [ ]  **print-symbol # print content of the specified binary symbol**
- [ ]  **max-worker-lifetime # reload workers after the specified amount of seconds (default is disabled)**
- [ ]  **lock-engine # set the lock engine**
- [ ]  **signal # send a uwsgi signal to a server**
- [ ]  **pidfile # pidfile**
- [ ]  **uid # setuid to the specified user/uid**
- [ ]  gid # setgid to the specified group/gid
- [ ]  **limit-nproc # limit the number of spawnable processes**
- [ ]  **no-orphans # automatically kill workers if master dies (can be dangerous for availability)**
- [ ]  **enable-metrics # enable metrics subsystem**
- [ ]  **stats-server # enable the stats server on the specified address**
- [ ]  **wsgi-accept-buffers # accept CPython buffer-compliant objects as WSGI response in addition to string/bytes**
- [ ]  How lazy apps affect performance ?

## Minimal Appliation

```python
# app.py
from flask import Flask, make_response, jsonify

app = Flask(__name__)


@app.get(url_prefix='/healthcheck')
def health_check():
    payload = {
        'status': 'alive',
        'message': 'Hello World'
    }
    return make_response(jsonify(payload), 200)

```

## Running

```bash
flask run --host localhost --port 5000
```

`--debug`

```python
# main.py
from app import app

if __name__ == '__main__':
    app.run(host='localhost', port=5000, debug=True)

```

Be aware with the prompt error:

```text
Do not use the development server when deploying to production. It is intended for use only during local development. It is not designed to be particularly secure, stable, or efficient.
```

This happens because flask does not come with one HTTP server built in, because of that the uWSGI is required.

## Requests Objects

### request

All the request incomming data is in `request` object, it can be called every where on the code and it's tread safe.

the data inside each attribute is a dict, always prefer accessing it by .get(), if the key is accessed directly and does not exists, it raises an error:

```text
In that case a special KeyError is raised. You can catch it like a standard KeyError but if you don’t do that, a HTTP 400 Bad Request error page is shown instead. So for many situations you don’t have to deal with that problem.
```

```python
from typing import Dict

from flask import Flask, request, make_response, jsonify

app =  Flask(__name__)

@app.get('/path')
def get_function() -> Dict:
    method: str = request.method
    headers: dict = request.headers
    cookies = request.cookies

    if method == 'GET':
        args: dict = request.args
       
    if method == 'POST':
        data: dict = request.get_json()
    
    return make_response(jsonify({'some': 'thing'}), 200)

```

### session

These objects are used to store data trought multiple requests, it can be treated as a dict.

To use session is a must have to set `secret_key` to the flask app, as it make de cookies criptografy, the value is a byte string.

```python
from flask import Flask, session

app = Flask(__name__)
app.secret_key = b'_5#y2L"F4Q8z\n\xec]/'

@app.get('/path')
def get_function() -> Dict:

    session['key'] = True

    return make_response(jsonify({'some': 'value'}), 200)

```

### Path Parameters

We can pass path parameters by adding `/<variable>` or `/<type:variable>` if you want data validation. The allowed types are: `string`, `int`, `float`, `path`, `uuid`.

```python
from typing import Dict

from flask import Flask, request, make_response, jsonify

app =  Flask(__name__)

@app.route('/var/<string:some>', methods=['GET'])
def var_route(some: str) -> Dict:
    payload = {'some': some}
    return make_response(jsonify(payload), 200)

```


## Routing

To work with distributed routing flask uses `Blueprint` object

```python
from flask import Flask, Blueprint

app = Flask(__name__)

child_route = Blueprint('/child')
parent_route = Blueprint('/parent')
parent_route.register_blueprint(child_route)

app.register_blueprint(parent_route)

```

`http://localhost:3000/parent/child`

## Testing Client

```python
# conftest.py
import pytest

from app import app


@pytest.fixture()
def test_app():
    app.config.update({"TESTING": True})
    # other setup can go here
    with app.app_context():
        yield app
    # clean up / reset resources here


@pytest.fixture()
def client(test_app):
    with tester.test_client() as client:
        yield client
```


```python
# tests/routes/test_health_check.py
import pytest


def test_health_check_then_return_200(client):
    # arrange
    HEALTH_CHECK_ROUTE = '/health_check'
    # act
    response = client.get(HEALTH_CHECK_ROUTE)
    # assert
    assert response.status_code == 200
    assert response.json['status'] == 'alive'
    assert response.json['message'] == 'Hello World!'
```
