# RabbitMQ Setup
NOTE: This uses CentOS
1. Switch to super user and then update patches and EPEL
```
sudo su -
yum update -y
yum install epel-release -y
```

2. Install Dependencies that will enable us install rabbitMQ
```
yum install wget -y
cd /tmp/
wget http://packages.erlang-solutions.com/erlang-solutions-2.0-1.noarch.rpm
sudo rpm -Uvh erlang-solutions-2.0-1.noarch.rpm
```
3. Install RabbitMQ Server. 
```
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash
sudo yum install rabbitmq-server -y
```
4. start, enable and check RabbitMQ status
```
systemctl start rabbitmq-server
systemctl enable rabbitmq-server
systemctl status rabbitmq-server
```
5. Configure  RabbitMQ. Here create a user called `test`, and password should be `test` as well. We are also going to tag our user as `administrator`. 
```
cd ~
echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config
rabbitmqctl add_user test test
rabbitmqctl set_user_tags test administrator
```
6. All done restart RabbitMQ using the command `systemctl status rabbitmq-server` once thats running, you can `exit` and `logout` of `rmq01`


