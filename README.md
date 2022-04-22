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
apt-get install python3-pip
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

@app.route('/user')
def hello_secured_user():
    message = "Hello secured User!"
    return message
```

```
export FLASK_APP=main.py
```

```
flask run
```

mTLS Configuration

```
cd /etc/ssl/
```

```
mkdir selfsignedcerts
```

```
cd selfsignedcerts/
```

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout server.key -out server.crt
```

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout client.key -out client.crt
```

```
openssl pkcs12 -export -out server.p12 -inkey client.key -in client.crt
```
or
```
 openssl pkcs12 -export -out server.p12 -inkey client.key -in client.crt -certfile certs/ca-certificates.crt
```

```
cp /etc/nginx/sites-enabled/default default-backup
```

```
vim cp /etc/nginx/sites-enabled/default
```

```
server {
  listen 443 ssl default_server;
  listen [::]:443 ssl default_server;

  ssl_certificate         /etc/ssl/selfsignedcerts/server.crt;
  ssl_certificate_key     /etc/ssl/selfsignedcerts/server.key;
  ssl_client_certificate  /etc/ssl/selfsignedcerts/client.crt;
  ssl_verify_client       on;

  root /home/tester/wfng;

  server_name _;

  location /user {
    if ($ssl_client_verify != "SUCCESS") { return 403; }

    proxy_pass http://localhost:5000/user;
  }

  location / {
    proxy_pass http://localhost:5000/;
  }
}
```

```
nginx -t
```

```
nginx -s reload
```
JMeter Error
```
<html>
<head><title>400 The SSL certificate error</title></head>
<body>
<center><h1>400 Bad Request</h1></center>
<center>The SSL certificate error</center>
<hr><center>nginx/1.18.0 (Ubuntu)</center>
</body>
</html>
```

Download the server.p12 and configure

```
javax.net.ssl.keyStore=<your_JKS_filename.jks>
javax.net.ssl.keyStorePassword=yourJKSpassword
```


