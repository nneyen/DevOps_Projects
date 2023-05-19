# Memcache Setup
Here we will install, start and enable memcache on port 11211
1. Switch to root user by running the command `sudo su -`
2. Next we will update our OS patches and also download the EPEL repository
```
yum update -y
yum install epel-release -y
```
3. start, enable and check for memcache status
```
systemctl start memcached
systemctl enable memcached
systemctl status memcached
```
4. Set memcache to listen on TCP port `11211` and UDP port `11111`
```
memcached -p 11211 -U 11111 -u memcached -d
```
To validate that memcached is running on the right port run the command 
```
ss -tunlp | grep 11211
```
you should see this image :
![Validate Memcached Ports](images/check_memcached_connection.png)

If this is it then you are done, you can `exit` and `logout` of  `mc01`




