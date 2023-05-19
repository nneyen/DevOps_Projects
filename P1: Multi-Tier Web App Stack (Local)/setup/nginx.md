# NGINX Setup 
This is an Ubuntu Server, so the setup will be slightly different 

1. Switch to super user
```
sudo su -
```
2. Update OS Patcjes
```
apt update && apt upgrade
```
3. Install NGINX
```
apt install nginx -y
```
4. Create the configuration file called `vproapp` in the directory `/etc/nginx/sites-available/`. This file will be used to redirect request from nginx to tomcat server from port `8080`. (NGINX is acting as a Load Balancer)
- first let's get into that directory 
```
vim /etc/nginx/sites-available/vproapp
```
- update the contents with the information below
```
upstream vproapp {
    server app01:8080;
}
server{
    listen 80;
location /{
    proxy_pass http://vproapp;
}
}
```
5. Remove the default nginx config file:
```
rm -rf /etc/nginx/sites-enabled/default
```

6. create a link to activate the website
```
ln -s /etc/nginx/sites-available/vproapp /etc/nginx/sites-enabled/vproapp
```
7. restart nginx and then `logout` of the server

```
systemctl restart nginx
```
