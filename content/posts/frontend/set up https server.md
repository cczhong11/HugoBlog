---
title: "How to set up HTTPS server with flask with personal domain"
date: "2022-10-19"
description: "e2e to set up HTTPS server with python flask app"
categories:
    - "SSL"
    - "HTTPS"
---

# Background

I currently has a personal website that hosted in the github page with React. In the React I also call the backend server with RESTful API. 

![](/content/posts/frontend/https.png)

My backend server is using AWS ec2 and I just used Python Flask for easy use.

In the past, I just expose the flask app to a port and in the React App to call this `http://ip:port` to get the data.

However, this method did not work properly if the github pages are in https because it would be fixed http and https. So I need to set up https in the backend server.

# HTTPS introduction

HTTPS (HTTP over SSL) refers to the combination of HTTP and SSL to implement secure communication between a Web browser and a Web server. The HTTPS capability is built into all modern Web browsers. Its use depends on the Web server supporting HTTPS communication.

This showed the flow for HTTPS communication. 

![](https://tczimg.s3.amazonaws.com/vscode/4d47d6abb64d4e70a358bb75133fa668.png)

In short, in the server side we need a certificate and private key to enable https.

# How to

There are lots of answers in the stackoverflow talking about [how to add https to flask](https://stackoverflow.com/questions/29458548/can-you-add-https-functionality-to-a-python-flask-web-server). However it is only useful in the local environment and the self generated certificate would not sufficient in the prod environment. Chrome will show the error `NET::ERR_CERT_INVALID` and you still could not use the https web site.

This [post](https://blog.miguelgrinberg.com/post/running-your-flask-application-over-https) gave me a good idea how to set up the https successfully.

So in the following example, I talked about how to set up the HTTPS with cloudflare.

1. Give your server a sub domain in the cloudflare DNS. This step is necessary since the certificate only works with domain, not with IP.
2. Get a origin certificate from the cloudflare.
![](https://tczimg.s3.amazonaws.com/vscode/054120d2c8a141989d3ee3316cff0572.png)
3. you also need an edge certificate if you need CDN.
4. set up your nginx.

```
$ sudo vim /etc/nginx/sites-enabled/default

server {
    listen 443 ssl;
    listen [::]:443 ssl;
    ssl_certificate /home/path/server.cert;
    ssl_certificate_key /home/path/server.key;

    location /path/ {
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header HOST $http_host;
                proxy_pass http://127.0.0.1:111/path/;
                proxy_redirect off;
    }
}
```
Add the following to your config and change the port number and route to fit your own server.

Run `sudo /etc/init.d/nginx reload`

5. Run your flask app
6. Update your url in the react 
7. Depoly the page.

Then you should see the https working in your website

# Summary

This problem bothered me long time and it took me several tries to make it work. Understanding the Https concept really helped me to finally make it work.