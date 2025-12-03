#  Deploy WordPress + MySQL with Docker

This project demonstrates how to deploy **WordPress** with a **MySQL** database using Docker.  
The setup runs two containers:
- **MySQL** → stores WordPress data
- **WordPress** → web application accessible via browser


## Steps to Deploy

### 1. Run MySQL Container
```bash
docker run -d --name mydbcont \
  -e MYSQL_ROOT_PASSWORD=pass@123 \
  -e MYSQL_DATABASE=wordpressdb \
  mysql
```
This creates a MySQL container with:
- Root password: pass@123
- Database: wordpressdb

### 2. Run WordPress Container (linked to MySQL)
```bash
docker run -d --name wordpresscont \
  -p 80:80 \
  -e WORDPRESS_DB_HOST=mydbcont:3306 \
  -e WORDPRESS_DB_USER=root \
  -e WORDPRESS_DB_PASSWORD=pass@123 \
  -e WORDPRESS_DB_NAME=wordpressdb \
  --link mydbcont:mysql \
  wordpress
```
- -p 80:80 → exposes WordPress on server’s port 80
- --link → connects WordPress to MySQL container (legacy method, but works)

###  3. Access WordPress in Browser

```bash
http://<EC2_PUBLIC_IP>/
```

Complete setup form:
- Site Title → any
- Username → any
- Password → pass@123
- Email → your email
Click Install WordPress → then login.

### 4. Verify Database Changes

SSH into your EC2 instance:
```bash
ssh ec2-user@<EC2_PUBLIC_IP>
sudo su
docker exec -it mydbcont bash
mysql -u root -p
# Enter: pass@123
```

Inside MySQL:
```bash
use wordpressdb;
show tables;
```

Expected tables:
- wp_posts
- wp_users
- wp_options

Example:
```bash
select * from wp_posts;
```

This displays WordPress posts/pages created in the site.

### 5. Outcome
- WordPress site accessible via browser
- Data persisted in MySQL database (wordpressdb)
- Verified by checking WordPress tables
