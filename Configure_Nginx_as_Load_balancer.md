## Configure Nginx as Load Balancer

On this demo, we will be configuring load balancer for nginx on our ```CentOS``` machine.
<br>

First, let's update our repository and install ```nginx``` on our machine using the ```yum``` command For our load balancing server.
```
yum update -y && yum install nginx -y
```
<br>

After the installation, navigate and edit the ```/etc/nginx/nginx.conf``` using any text editor. for this demo, we will be using the ```vi``` editor.
```
vi /etc/nginx/nginx.conf
```
<br>

Third, Let's paste the following. This is the default load balancing configuration. By default, ```Nginx``` will distribute traffic among them using [round-robin](https://www.cloudflare.com/learning/performance/types-of-load-balancing-algorithms/).
```
http {
    upstream myapp1 {
        server <IP ADDRESS of the nginx server>;
        sserver <IP ADDRESS of the nginx server>;
    }

server {
        listen 80;

        location / {
            proxy_pass http://myapp1;
        }
    }
}

In
```
<br>

*Explaination:*

| Commands |  Description                       |
| :-------- | :-------------------------------- |
| `http { ... }`      | 	It defines the HTTP context for NGINX configuration. All web-related settings go here. |
| `-upstream myapp1 { ... }`      | 	Declares a group of backend servers named ```myapp1``` for load balancing. |
| `server <IP ADDRESS of the nginx server>;`      | This is where you add the ```nginx``` server that  you want to load balance. |
| `server {...}`      | Defines a virtual server block to handle incoming HTTP requests. |
| `listen 80;`      | 	Tells NGINX to listen on port 80 (standard HTTP port). |
| `proxy_pass http://myapp1;`      | 	Forwards requests to the upstream group ```myapp1```. |
<br>

Once done, exit on the ```vi``` editor by pressing the ```Esc``` then type ```:wq!``` to save the configuration.
<br>

Next, check the configuration using:
```
nginx -t
```
<br>

You should be able to get a similar result:
<br>
<img width="739" height="47" alt="image" src="https://github.com/user-attachments/assets/b3ee8234-14d1-497a-b9ba-0eaa4b0f2d45" />
<br>

Restart the ```Nginx``` service.
```
systemctl restart nginx
```
<br>




