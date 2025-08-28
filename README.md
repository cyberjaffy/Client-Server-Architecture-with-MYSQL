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

text

---

## Step 3 — Install and Configure MySQL Server

Update system and install MySQL server:

sudo apt update && sudo apt upgrade -y
sudo apt install mysql-server -y

text

Ensure MySQL service is running and enabled:

sudo systemctl start mysql
sudo systemctl enable mysql
sudo systemctl status mysql

text

---

## Step 4 — Configure MySQL for Remote Access

Edit MySQL config file to listen on all interfaces:

sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf

text

Find and change:

bind-address = 0.0.0.0

text

Save and exit (`Ctrl+O`, then `Ctrl+X`).

Restart MySQL service to apply changes:

sudo systemctl restart mysql

text

---

## Step 5 — Setup MySQL User with Remote Access

Launch MySQL Shell:

sudo mysql

text

Create a user for remote access (replace placeholders):

CREATE USER 'remote_user'@'<Client_Private_IP>' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON . TO 'remote_user'@'<Client_Private_IP>' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;

text

---

## Step 6 — Configure Firewall

If UFW firewall is active, allow MySQL port from client:

sudo ufw allow from <Client_Private_IP> to any port 3306
sudo ufw reload

text

---

## Step 7 — Connect from MySQL Client Instance

SSH into client instance:

ssh -i /path/to/key.pem ubuntu@<Client_Public_IP>

text

Install MySQL client tools:

sudo apt update
sudo apt install mysql-client -y

text

Connect to server:

mysql -h <MySQL_Server_Private_IP> -u remote_user -p

text

Enter password when prompted.

---

## Step 8 — Verify MySQL Client-Server Functionality

Within MySQL shell, create test database and table:

CREATE DATABASE testdb;
USE testdb;

CREATE TABLE sample (
id INT AUTO_INCREMENT PRIMARY KEY,
name VARCHAR(100),
email VARCHAR(100)
);

INSERT INTO sample (name, email) VALUES ('Alice', 'alice@example.com'), ('Bob', 'bob@example.com');

SELECT * FROM sample;

text

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
