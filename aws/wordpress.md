# Create and Host a Wordpress Website on AWS

## Create an EC2 instance

You need a Virtual Machine to run your website.

- Launch EC2 instance
- Create an Elastic IP address
- Associate Elastic IP address to EC2

## Web Server: Apache

A software that can handles HTTP requests

1. SSH inside EC2 instance

```
ssh -i "[certification-name].pem" ubuntu@ec2-54-208-10-99.compute-1.amazonaws.com
```

2. Install Apache

```
sudo apt install apache2
```

3. Install PHP Runtime and its connectors

```
sudo apt install php libapache2-mod-php php-mysql
```

## DB: MySQL Server

We need to install mysql on our VM

1. Install MySQL Server

```
sudo apt install mysql-server
```

2. Change MySQL authentication to native password

a. Log in to `mysql` prompt as `root`

```
sudo mysql -u root
```

b. Run this command (providing it with the password)

```
ALTER USER 'root'@localhost IDENTIFIED WITH mysql_native_password BY '<password>';
```

3. Create new MySQL user `wp_user`

```
CREATE USER 'wp_user'@localhost IDENTIFIED BY '<password>';
```

4. Configure new database and connect it to Wordpress

```
CREATE DATABASE wp;
```

5. Assign all privileges to `wp_user`

```
GRANT ALL PRIVILEGES ON wp.* TO 'wp_user'@localhost;
```

Update apt-get if any bug happens:

```
sudo apt-get update
```

## Wordpress

A software written in PHP that allows website creation

- Go to /tmp directory
- Go to [wordpress.org](https://wordpress.org/) and get the download link of the tar.gz file
- Run `wget`

```
wget https://wordpress.org/wordpress-6.3.2.tar.gz
```

- Unzip it with `tar -xvf`

```
tar -xvf <name>.tar.gz
```

- Move the folder (wordpress) to the document root of Apache

```
sudo mv wordpress /var/www/html/
```

- Got to `http://<ip-address>/wordpress` to install wordpress
- You might get an error, you need to create and configure `wp-config.php`
  a. First cd into `wordpress`

```
cd /var/www/html/
cd wordpress
```

b. Create a `wp-config.php` file and paste what's in the message

- Continue installing wordpress
- Fix root directory to serve the wordpress website
  a. Got to apache `sites-available` to config

```
cd /etc/apache2/sites-available/

```

b. Configure `DocumentRoot` in `000-default.conf `

```
sudo nano 000-default.conf
```

Add `/wordpress` to `DocumentRoot`

```
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/wordpress
```

c. Restart Apache

```
sudo systemctl restart apache2
```

## Domain name

## SSL Certificate

Make the website work over HTTPS, we need to install `certbot`

```
sudo apt install certbot python3-certbot-apache
```

Run the tool:

```
sudo certbot --apache
```

Enter email, and agree. Then insert the domain name.

# Refs

- https://youtube.com/watch?v=8Uofkq718n8
- https://youtu.be/8Uofkq718n8?si=a1q6r6kWzmxy8437&t=865
- https://maheshwaghmare.com/wordpress/how-to/fix-perform-requested-action/#google_vignette
- https://wpastra.com/docs/xmlreader-support-missing-starter-templates-for-the-administrators/
- https://wpastra.com/docs/curl-support-missing-for-the-administrators/
