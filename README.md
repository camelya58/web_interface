# web_interface
Instruction for running the web interface and configuring nginx for microservices for Windows.

## Step 1
Setup the [Nodejs](https://nodejs.org/en/).

## Step 2
To install the Angular CLI, open a terminal window and run the following command:
```
npm install -g @angular/cli
```

If you have an error about bad network settings you need to create the file .npmrc for proxy using the following command:
```
npm config set proxy http://...
npm config set https-proxy https://...
```
And repeat the command.

## Step 3
Download the [nginx](https://nginx.org/ru/download.html).
```
cd c:\
unzip nginx-1.21.1.zip
cd nginx-1.21.1
start nginx
```

To see the process of nginx use the folowing command:
```
C:\nginx-1.21.1>tasklist /fi "imagename eq nginx.exe"
```

If you don't see any process go to the logs and see the error.
It can be "bind() to 0.0.0.0:80 failed". That means that this port is busy, so you need to choose another port and write it in the config file config/nginx.conf:
```
server {
  server_name localhost;
  listen 8080;
  ...
}
```

## Step 5
Write redirect for microservices to the file nginx.conf
```
location /service1 {
        proxy_pass      http://localhost:8090/service1;     
}

location /service2 {
        proxy_pass      http://localhost:8091/service2;     
}
```

## Step 6
Build and run the project.
```
npm run start
```

## Step 7
If you want to add self-signed certificate to nginx complete the following steps.
- generate the certificate and private key (use Git Bash and create the directories):
```
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout "C:\nginx-1.21.1\ssl\private\nginx.key" -out "C:\nginx-1.21.1\ssl\certs\nginx.crt"
```
Where:
*req* — the command to use X.509;
*x509* — the command to create the self-signed certificate;
*nodes* — the command to miss the password phrase;
*days* — the command to set the validity of the certificate;
*newkey* (rsa:2048) — the command to generate the nwe private key with RSA algorithm for 2048 bit;
*keyout* — the location of the private key;
*out* - the location of the certificate.

Warning! CN= your domain name (localhost)

- add to nginx.conf file:
```
server {
  server_name localhost;
  listen 8080;
  listen 443 ssl;
  
  ssl_certificate  ../ssl/certs/nginx.crt;
  ssl_certificate_key ../ssl/private/nginx.key;
  
  location / {
      root html;
      index index.html index.htm;
  }
  
  location /service1 {
        proxy_pass      https://localhost:8090/service1;     
  }

  location /service2 {
        proxy_pass      https://localhost:8091/service2;     
  }
  ...
}
```
