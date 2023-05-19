## MySQL Setup
1. switch to root user and update all packages. Updating OS with the latest patches is good practice.
```
sudo su -
yum update -y
```
2. Setup Password variable for the database like this `DATABASE_PASS="admin123"` you can call the password whatever you like too

- to confirm that the password has been set, use the command `echo $DATABASE_PASS`

3. We have to make sure that the password is permanent so we do not lose it when we logout of the vm. to do this add it to  `/etc/profile` and update it 
```
vi /etc/profile
source /etc/profile
```
4. Install EPEL (Extra packages for Enterprice Linux) repository. EPEL offers additional software packages for CentOS and Red Hat Enterprise Linux (RHEL) systems. By installing the EPEL repository, users can access a wide range of software packages that are not included in the default repositories.
```
yum install epel-release  -y
```
5. install git and maria db package. Installing git will make it possible to clone the repository into this server
```
yum install git mariadb-server -y
```
6. Start and enable the mariadb
```
systemctl start mariadb
systemctl enable mariadb
```
7. You can confirm that your mariadb is running the command `systemctl status mariadb` if it is running you should see `active(running)`.

8. Run the sql installation script. 
```
mysql_secure_installation
```
9. Follow the following steps to complete the installation. Set the db root password to `admin123` or if you chose a different password in step 2, you can use your password
```
Set root password? [Y/n] Y
New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
... Success!

By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.
Remove anonymous users? [Y/n] Y
... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.
Disallow root login remotely? [Y/n] n
... skipping.

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.
Remove test database and access to it? [Y/n] Y
- Dropping test database...
... Success!
- Removing privileges on test database...
... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.
Reload privilege tables now? [Y/n] Y
... Success!
```
you should see the following messages *All done! if you've completed all of the above steps, your MariaDB installation should now be secure*
10. Connect to your db and if you are able to log in you will see a Welcome Message. once this is confirmed you can exit

```
mysql -u root -p
exit
```
11. Clone the source code into your server, and cd into the resource directory. We need to find the sql queries `db_backup.sql`to use in initialising our database with these queries
```
git clone -b local-setup https://github.com/rumeysakdogan/vprofile-project.git
cd vprofile-project/src/main/resources/
```
12. Setup DB name and users (our database name is `accounts` and the user is `admin` )
```
mysql -u root -p"$DATABASE_PASS" -e "create database accounts"
mysql -u root -p"$DATABASE_PASS" -e "grant all priviledges on acconts.* TO 'admin'@'app01' identified by 'admin123' "
cd ../../..
mysql -u root -p"$DATABASE_PASS" accounts < src/main/resources/db_backup.sql
mysql -u root -p"$DATABASE_PASS" -e "FLUSH PRIVILEGES"
```
13. Let's log into the database to chekc if our SQL queries can actually startup a database
```
mysql -u root -p"$DATABASE_PASS"
MariaDB[(none)] > show databases;
MariaDB[(none)] > use accounts;
MariaDB[(none)] > show tables;
```
if it works on your end type `exit` to leave the databel
14. Now we will restart mariadb-server since we are done with the setup using the command `systemctl restart mariadb` then `logout`









