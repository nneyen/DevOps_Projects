# Tomcat Setup
NOTE: This uses CentOS

1. Switch to super user and then update patches and EPEL
```
sudo su -
yum update -y
yum install epel-release -y
```
2. install the dependencies necessary for Tomcat - The dependencies needed are **Java jdk v1.8.0** and **Maven**
```
yum install java-1.8.0-openjdk -y
yum install git maven wget -y
```
3. Switch to tmp folder and download and extract Tomcat
```
cd /tmp
wget https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.37/bin/apache-tomcat-8.5.37.tar.
tar xzvf apache-tomcat-8.5.37.tar.gz
```
4. Add tomcat user called `tomcat` but we will first create a home directory called `/usr/local/tomcat8
```
useradd --home-dir /usr/local/tomcat8 --shell /sbin/nologin tomcat
```
5. Copy our data into the home directory we created in step 4
```
cp -r /tmp/apache-tomcat-8.5.37/* /usr/local/tomcat8/
ls /usr/local/tomcat8
```
6. Change the ownership of the home directy to `tomcat`
```
chown -R tomcat.tomcat /usr/local/tomcat8
```
7. Setup systemd for tomcat. In this step
- first create a file using the command :
```
vi /etc/systemd/system/tomcat.service
```

- then update the file with this content :
```
[Unit]
Description=Tomcat
After=network.target

[Service]
User=tomcat
WorkingDirectory=/usr/local/tomcat8
Environment=JRE_HOME=/usr/lib/jvm/jre
Environment=JAVA_HOME=/usr/lib/jvm/jre
Environment=CATALINA_HOME=/usr/local/tomcat8
Environment=CATALINE_BASE=/usr/local/tomcat8
ExecStart=/usr/local/tomcat8/bin/catalina.sh run
ExecStop=/usr/local/tomcat8/bin/shutdown.sh
SyslogIdentifier=tomcat-%i

[Install]
WantedBy=multi-user.target
```
- run the following command since we have made changes to `/etc/systemd/system/
```
systemctl deamon-reload
```
8. enable, start and check tomcat status
```
systemctl enable tomcat
systemctl start tomcat
systemctl status tomcat
```
9. We will now Build and Deploy Tomcat

- clone the source code and cd into the `vprofile-project` directory
```
git clone -b local-setup https://github.com/rumeysakdogan/vprofile-project.git
cd vprofile-project
```

- We will update our configuration file that is connected to our backend services (i.e. memcache, rabbitmq and database services)
```
vi src/main/resources/application.properties
```
here we are checking a few things :
- DB Configuration (hostname:`db01`) : user is `admin` and password is `admin123`
- Memcache Configuraton (hostname: `mc01`) should be listening on port `11211`
- RabbitMQ Configuration (hostname: `rmq01`) confirm that but the user and password are called `test`

```
#JDBC Configutation for Database Connection
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://db01:3306/accounts?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull
jdbc.username=admin
jdbc.password=admin123

#Memcached Configuration For Active and StandBy Host
#For Active Host
memcached.active.host=mc01
memcached.active.port=11211
#For StandBy Host
memcached.standBy.host=127.0.0.2
memcached.standBy.port=11211

#RabbitMq Configuration
rabbitmq.address=rmq01
rabbitmq.port=5672
rabbitmq.username=test
rabbitmq.password=test
```
you can save and exit if all is good you should be back in the `vprofile-project` directory
10. We will now build the code
```
mvn install
```
- this creates an artifact `/tmp/vprofile-project/target/vprofile-v2.war` which you can view here
```
cd target/
ls
```
11. Deploy the artifact `vprofile-v2.war` to our Tomcat Server
- first stop tomcat and remove the default app that is located in `/usr/local/tomcat8/webapps/ROOT`
```
systemctl stop tomcat
systemctl status tomcat
rm -rf /usr/local/tomcat8/webapps/ROOT
```
- copy our artifact from target directory,  into the previous location that contained the default app. You can use the command `ls ls /usr/local/tomcat8/webapps/` to see if it is successfully copied
```
cd ..
cp target/vprofile-v2.war /usr/local/tomcat8/webapps/ROOT.war
systemctl start tomcat
```
- update ownership and restart tomcat
```
chown tomcat.tomcat usr/local/tomcat8/webapps -R
systemctl restart tomcat
```
you can now `logout`

