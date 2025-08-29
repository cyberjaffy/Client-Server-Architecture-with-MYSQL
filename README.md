text
# MySQL Client-Server Architecture on AWS EC2

## Overview

This project implements a client-server architecture using MySQL Database Management System (DBMS) on AWS Cloud. It involves setting up two Linux-based EC2 instances:

- **MySQL Server**: Hosts the MySQL database and accepts remote connections.
- **MySQL Client**: Connects to the MySQL server remotely to perform database operations.

Through this setup, I learned practical skills in configuring networking, database security, and managing remote database access on cloud infrastructure.

---

## Prerequisites

- AWS account with permissions to create and manage EC2 instances and security groups.
- SSH key pair to connect securely to Linux instances.
- Basic Linux command line knowledge.
- Familiarity with MySQL fundamentals.

---

## Step 1 — Launch Two Ubuntu EC2 Instances

1. Log into AWS Management Console and navigate to EC2.
2. Launch two **Ubuntu Server LTS** instances (e.g., Ubuntu 22.04):
   - Select instance type: **t2.micro** for cost-effectiveness.
   - Create or use an existing SSH key pair.
3. Create security groups:
   - **Allow SSH (port 22)** from your IP on both instances.
   - On the MySQL Server instance:
     - Allow **MySQL port (3306)** access **only from the Client instance’s private IP**.
4. Note the Public and Private IP addresses of both instances.

---

## Step 2 — Connect to MySQL Server Instance

Open your terminal or Git Bash and run:

ssh -i /path/to/key.pem ubuntu@<MySQL_Server_Public_IP>

<img width="1173" height="282" alt="image" src="https://github.com/user-attachments/assets/d8f3c016-82e6-4c4f-a106-a775ad60f38a" />

---

## Step 3 — Install and Configure MySQL Server

Update system and install MySQL server:

sudo apt update && sudo apt upgrade -y
sudo apt install mysql-server -y

<img width="1282" height="857" alt="image" src="https://github.com/user-attachments/assets/621ce6f6-fec1-4d2d-b3fb-1e2bc19b87d1" />


Ensure MySQL service is running and enabled:

sudo systemctl start mysql
sudo systemctl enable mysql
sudo systemctl status mysql

<img width="1193" height="500" alt="image" src="https://github.com/user-attachments/assets/9cece7dd-fac3-45a2-816d-8a000ee00e40" />


---

## Step 4 — Configure MySQL for Remote Access

Edit MySQL config file to listen on all interfaces:

sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf

<img width="1297" height="884" alt="image" src="https://github.com/user-attachments/assets/cc1a5563-7167-4681-9150-b69d42188594" />


Find and change:

bind-address = 0.0.0.0

<img width="1281" height="861" alt="image" src="https://github.com/user-attachments/assets/537edf62-0c51-4c9e-8f1b-e487551529f1" />


Save and exit (`:wq`, then `esc`).

Restart MySQL service to apply changes:

sudo systemctl restart mysql

<img width="1311" height="60" alt="image" src="https://github.com/user-attachments/assets/51f84c14-c9d2-4577-8a9e-1f68c09aa441" />


---

## Step 5 — Setup MySQL User with Remote Access

Launch MySQL Shell:

sudo mysql

<img width="1313" height="538" alt="image" src="https://github.com/user-attachments/assets/8223df00-c5f8-4fd8-b47d-1897841216fc" />


Create a user for remote access (replace placeholders):

CREATE USER 'remote_user'@'<Client_Private_IP>' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON *.* TO 'remote_user'@'<Client_Private_IP>' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;

<img width="1292" height="508" alt="image" src="https://github.com/user-attachments/assets/84b434e0-ff03-4755-bce0-d136e336fc65" />


---

## Step 6 — Configure Firewall

If UFW firewall is active, allow MySQL port from client:

On the AWS portal Allow port 3306 from all locations 


---

## Step 7 — Connect from MySQL Client Instance

SSH into client instance:

ssh -i /path/to/key.pem ubuntu@<Client_Public_IP>

<img width="1195" height="150" alt="image" src="https://github.com/user-attachments/assets/233d8139-dbea-4ad0-947e-b542d9d513c1" />


Install MySQL client tools:

sudo apt update
sudo apt install mysql-client -y

text

Connect to server:

mysql -h <MySQL_Server_Private_IP> -u remote_user -p

text

Enter password when prompted.
<img width="1055" height="413" alt="image" src="https://github.com/user-attachments/assets/9f4fb80c-a42b-4952-82a0-65c4e835d9b5" />

---

## Step 8 — Verify MySQL Client-Server Functionality

Within MySQL shell, create test database and table:

CREATE DATABASE testdb;
USE testdb;

CREATE TABLE sampletable (
id INT AUTO_INCREMENT PRIMARY KEY,
name VARCHAR(100),
email VARCHAR(100)
);

INSERT INTO sampletable (name, email) VALUES ('Alice', 'alice@example.com'), ('Bob', 'bob@example.com');

SELECT * FROM sampletable;

<img width="1240" height="706" alt="image" src="https://github.com/user-attachments/assets/6618696e-7e5a-427e-86c9-9f706b2f07a0" />


---

## Step 9 — Secure and Maintain Your Setup

- Avoid using root for remote connections.
- Use strong passwords and limited user privileges.
- Configure security groups strictly to allow only necessary traffic.
- Keep instances updated regularly.
- Set up backups for databases (e.g. `mysqldump`, AWS snapshots).

---

## Troubleshooting

- **Remote connection refused or timed out:**  
  - Check security group rules and firewall settings.
  - Confirm MySQL is listening on the correct IP.
- **Access denied for user:**  
  - Verify user host and password.
  - Double-check grants with `SHOW GRANTS;` in MySQL.
    <img width="1308" height="800" alt="image" src="https://github.com/user-attachments/assets/f1b27ac6-3080-496c-b7fd-f67184c01835" />

- **MySQL service won’t start:**  
  - Inspect logs at `/var/log/mysql/error.log`.
  - Confirm configuration syntax is correct.

---

## Conclusion

This project provided hands-on experience with setting up and configuring a MySQL client-server architecture on AWS EC2 instances. It highlighted key aspects of cloud networking, database security, and remote access management essential for scalable and secure application deployments.

---

### Resources

- [MySQL Official Documentation](https://dev.mysql.com/doc/)
- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [Ubuntu Server Guide](https://ubuntu.com/server/docs)

---

Congratulations on successfully implementing your MySQL client-server setup on AWS!
