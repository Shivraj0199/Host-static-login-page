### **Static html login page app that connects to RDS (mysql) hosted on Ec2 instance served with nginx, secured using Certbot (Lets's encrypt) and using IAM authentication for secure RDS access also access to own domain**

#

1. **Frontend:** HTML + JavaScript login page 

2. **Backend:** Node.js server (API handler)

3. **Database:** Amazon RDS MySQL with IAM authentication

4. **Deployment:** EC2 (Ubuntu), Nginx, Certbot (HTTPS)

5. **Domain:** Your own (tech-connect.cloud)

6. **IAM:** EC2 IAM role with rds-db:connect permission

## Step 1: Create RDS MySQL Database

* **Go to RDS >** Create database

* **Select MySQL, Standard Create**

* **Enable:**

  * **DB Name:** login

  * **Master user:** admin

* **Enable IAM DB authentication**
* **Create and note the end point**

## Step 2: Create IAM role for Ec2

1.  **Go to ->** IAM Console -> Roles
2.  **Click create role**
3.  **Select trusted entity:**
   *  Choose aws service
   *  Use case : Ec2
   *  Click next

4. Skip aws managed permissions
5. Name the role
   * **Role name:** EC2RDSIAMROLE (Give any name you want)
   * **Click create role**

6. **Add inline policy to the role**
   * **Go back to the IAM ->** Roles
   * Click on your newly created role (EC2RDSIAMROLE)
   * **Scroll down to the permissions ->** Click add inline policy
   * **Choose :** Json
   * **Paste the IAM policy :**
   
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "rds-db:connect",
      "Resource": "arn:aws:rds-db:<region>:<account-id>:dbuser:<db-resource-id>/admin"
    }
  ]
}
```
   * Click reviewing policy
   * Name the policy : Allow Ec2 to RDS connect (Give any name)
   * Click create policy

## Step 3: Attach the IAM role to Ec2 Instance

* **If you  already launched ec2 instance go with following steps**

1. **Go to Ec2 console ->** Instances
2. Select your instance
3. **Click actions ->** Security -> Modify IAM role
4. Choose ``` EC2RDSIAMROLE``` from dropdown
5. Click update IAM role

## Step 4: Install mysql-client (on obuntu)

1. sudo apt update -y
2. sudo apt install mysql-client -y

## Step 5: Connect to your RDS

1. **mysql -h <your-rds-end-point> -u admin --enable-cleartext-plugin -p**

### Create database and table

1. create database login;
2. use login;
3. create table users (id int AUTO_INCREMENT PRIMARY KEY, username varchar(50), password varchar(50));
4. insert into users (username,password) values ('admin','1234');
5. select * from users;

## Step 6: Install all dependencies

1. sudo apt update -y
2. sudo apt install nodejs -y
3. sudo apt install npm -y
4. sudo apt install nginx -y (start and enable nginx)
5. sudo apt install git -y

## Step 7: Upload app code

1. git clone https://github.com/Shivraj0199/Login-html-app.git
2. cd Login-html-app/backend
3. npm install mysql2 express body-parser@aws-sdk/rds-signer
4. node server.js
5. sudo cp Login-html-app/frontend/index.html /var/www/html/index.html
6. sudo systemctl restart nginx
7. visit in browser ``` http://<ec2-public-ip>```

* **Nginx - Access via https://<ec2-public-ip>**
* **Express - Access via https://<ec2-public-ip>:5000**

## Step 8: Install PM2 (Server running backend)

1. sudo npm install -g pm2
2. pm2 start server.js --name login-backend
3. pm2 logs login-backend

* **If you are not inside the backend folder, then do this**

1. **pm2 start Login-html-app/backend/server.js --name login-backend**

## Step 8: Configure nginx as Reverse proxy

1. sudo nano /etc/nginx/sites-available/default

```
server {
  listen 80;
  server_name tech-connect.cloud www.tech-connect.cloud;

  location / {
    proxy_pass http://localhost:5000;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
  }
}
```
## Step 9: Connect domain to ec2

### Go to your domain register

1. **Find DNS setting**
2. **Add 'A' record**
 * **@ ->** ```<your-ec2-ip>```
 * **www ->** ```<your-ec2-ip>```

3. **Save and wait for DNS to propogate**

## Step 10: Install SSL certificate via certbot (Lets's Encrypt)

1. sudo apt update -y
2. sudo apt insatll python3-certbot-nginx -y
3. sudo certbot --nginx -d ```<your-domain-name>``` -d ```www.<your-domain-name>```
4. sudo certbot renew --dry-run 
   

