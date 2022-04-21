# nginx-mTLS-JMeter
mTLS demo in JMeter using nginx

# Steps

```
sudo apt-get update -y
```

```
apt-get install nginx -y
```

```
apt-get install python3-flask -y
```

```
vim main.py
```

```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world_app():
    message = "Hello non-secured World!"
    return message

@app.route('/')
def hello_secured_user():
    message = "Hello secured User!"
    return message
```

```
export FLASK_APP=main.py
```
