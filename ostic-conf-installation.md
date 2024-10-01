# OsTicket install and configuration on ubuntu 22.04

### Step 1: Update the System
_First, make sure your system is up to date:_

```bash
sudo apt update
sudo apt upgrade -y
```
### Step 2: Install Apache
_Install the Apache web server:_
```bash
sudo apt install apache2 -y
```
### Step 3: Install MySQL
_Install MySQL server:_

```bash
sudo apt install mysql-server -y
```
_After installation, secure MySQL:_
```bash
sudo mysql_secure_installation
```
You'll be prompted to set the root password and make some security adjustments.

### Step 4: Install PHP and Required Extensions
_Install PHP and the necessary extensions for osTicket:_
```bash
sudo apt install php libapache2-mod-php php-cli php-mysql php-xml php-imap php-mbstring php-intl php-gd php-json php-curl php-bz2 -y
```
### Step 5: Download and Configure osTicket
1. Download osTicket:

- _Navigate to the `/var/www/` directory:_
```bash
cd /var/www
```
- _Download the latest stable release of osTicket:_
```bash
sudo wget https://github.com/osTicket/osTicket/releases/download/v1.18.1/osTicket-v1.18.1.zip
```
- _Extract the downloaded file:_
```bash
sudo apt install unzip -y
sudo unzip osTicket-v1.18.1.zip -d osticket
```
2. Set Permissions:

- _Set the correct permissions for the osTicket directory:_

```bash
sudo chown -R www-data:www-data /var/www/osticket
sudo chmod -R 755 /var/www/osticket
```
3. Copy the Sample Configuration:
- _Copy the sample configuration file to the actual configuration file:_
```bash
sudo cp /var/www/osticket/upload/include/ost-sampleconfig.php /var/www/osticket/upload/include/ost-config.php
```
4. Set Permissions for the Configuration File:
```bash
sudo chmod 0666 /var/www/osticket/upload/include/ost-config.php
```
### Step 6: Configure Apache
1. Create a Virtual Host:

- _Create a new Apache configuration file for osTicket:_

```bash
sudo vim /etc/apache2/sites-available/osticket.conf
```
- _Add the following configuration:_

```apache
<VirtualHost *:80>
    ServerAdmin   admin@support.radianterp.in
    Servername    support.radianterp.in

    Redirect permanent / https://support.radianterp.in/
</VirtualHost>                 

<VirtualHost *:443>

    ServerAdmin  admin@support.radianterp.in
    Servername   support.radianterp.in
    DocumentRoot /var/www/osticket/upload
       
    # HTTP TO HTTPS REDIRECTION
    SSLEngine on
    SSLCertificateFile       /etc/apache2/ssl/radianterp.in/certificate.crt
    SSLCertificateKeyFile    /etc/apache2/ssl/radianterp.in/private.key
    SSLCertificateChainFile  /etc/apache2/ssl/radianterp.in/ca-bundle-client.crt
    
    # APACHE2 LOGS
    ErrorLog  ${APACHE_LOG_DIR}/support.radianterp.in.error.log
    CustomLog ${APACHE_LOG_DIR}/support.radianterp.in.access.log combined
      
    <Directory /var/www/osticket/upload>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```
Replace `example.com` with your domain or server's IP address.

2. Enable the osTicket Site and Rewrite Module:
```bash
sudo a2ensite osticket.conf
sudo a2enmod ssl
sudo a2enmod rewrite
sudo systemctl restart apache2 
```
### Step 7: Create MySQL Database and User
_1. Login to MySQL:_
```bash
sudo mysql -u root -p
```
_2. Create the Database:_
```sql
CREATE DATABASE osticket;
```
_3. Create a Database User:_
```sql
CREATE USER 'osticket'@'localhost' IDENTIFIED BY 'xxxxxxx';
```
_4. Grant All Privileges:_
```sql
GRANT ALL PRIVILEGES ON osticket.* TO 'osticket'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
### Step 8: Finalize osTicket Installation
1. Access osTicket in Your Web Browser:
- _Navigate to `http://your-server-ip-or-domain` in your browser._
- _You should see the osTicket installer._

2. Complete the Web-Based Installation:

- _Fill in the necessary details:_
   - _Database Name: `osticket`_
   - _MySQL Username: `osticket`_
   - _MySQL Password: `xxxxxxx`_
- _Follow the on-screen instructions to complete the installation._
3. Secure the Configuration File:
- _After the installation, set the correct permissions for the configuration file:_
```bash
sudo chmod 0644 /var/www/osticket/upload/include/ost-config.php
```
4. Remove the Setup Directory:
- _For security reasons, remove the setup directory:_
```bash
sudo rm -rf /var/www/osticket/upload/setup
```
### Step 9: Access and Configure osTicket
- _Admin Panel: Go to `http://your-server-ip-or-domain/scp` to access the admin panel and configure your osTicket system._ #
