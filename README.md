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

## Step 6
Write redirect for microservices to the file nginx.conf
```
location /service1 {
        proxy_pass      http://localhost:8090/service1;     
}

location /service2 {
        proxy_pass      http://localhost:8091/service2;     
}
```

## Step 5
Build and run the project.
```
npm run start
```
