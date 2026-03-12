# Java Application Deployment with Reverse Proxy on AWS
Project Overview

This project demonstrates how to deploy a Java-based Student Registration Web Application on AWS using a Reverse Proxy Architecture.

In many traditional systems, backend servers are directly exposed to the internet, which creates security risks. In this project, a reverse proxy server is used to forward client requests to the backend server while keeping the backend server protected.

The backend application runs on Apache Tomcat, the proxy server runs Nginx, and the student data is stored in Amazon RDS MySQL.

# Architecture

User Browser
↓
Reverse Proxy Server (Nginx)
↓
Backend Server (Apache Tomcat)
↓
Amazon RDS MySQL Database

Traffic Flow:

User sends request from browser

Request reaches Nginx reverse proxy

Nginx forwards request to backend Tomcat server

Java application processes the request

Student data is stored in Amazon RDS database

Response is returned to the user

# Step 1 – EC2 Infrastructure Setup

Two EC2 instances were created.

Instance        	     Role                     	Access
java-backend-server	     Tomcat Application Server	Private
reverse-proxy-server	Nginx Reverse Proxy      	Public

The reverse proxy server receives requests from the internet and forwards them to the backend server.

EC2 Instances Screenshot


<img width="1917" height="909" alt="intances" src="https://github.com/user-attachments/assets/d0bdfebb-b80c-4443-a028-ee4056f9d03f" />

# Step 2 – Security Group Configuration

Security groups were configured to secure the backend server.

Backend Security Group Rules:

Port           	Description
22	               SSH access
8080	               Allowed only from Proxy Server

This ensures that users cannot access the backend server directly.

Security Group Screenshot:

<img width="1920" height="1020" alt="instancesg" src="https://github.com/user-attachments/assets/00e94204-7f1d-4677-af5f-a3d7b947e109" />

backend security group
<img width="1910" height="906" alt="backend sg" src="https://github.com/user-attachments/assets/dac1ca74-21a7-40cb-8e5d-adae3cb04811" />

# Step 3 – Apache Tomcat Installation

Apache Tomcat was installed on the backend EC2 instance to host the Java application.

Commands used:

sudo yum update -y
sudo yum install java-11-amazon-corretto -y

Download and install Tomcat:

cd /opt
sudo wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.89/bin/apache-tomcat-9.0.89.tar.gz
sudo tar -xvzf apache-tomcat-9.0.89.tar.gz
sudo mv apache-tomcat-9.0.89 tomcat

Start Tomcat server:

sudo /opt/tomcat/bin/startup.sh
Tomcat Running Screenshot
<img width="1920" height="1020" alt="tomcat" src="https://github.com/user-attachments/assets/a09c9e19-d0b5-4655-96e6-a1bf202cdfb6" />

# Step 4 – Deploy Java Application

The Java web application (student.war) was deployed inside the Tomcat webapps directory.

/opt/tomcat/webapps/
Student Registration Form
![Student Registration Form](screenshots/student-form.png)

<img width="1920" height="1020" alt="student registration form" src="https://github.com/user-attachments/assets/454f0814-b840-4b75-83d6-a0b4be73d045" />
# Step 5 – Student Data Submission

Users can enter student details in the form and submit them to the backend application.

Example fields:

Student Name

Student Address

Student Age

Qualification

Percentage

Year Passed

Form Filled Screenshot:
<img width="1919" height="966" alt="Screenshot 2026-03-12 153217" src="https://github.com/user-attachments/assets/c9551b13-0d79-4c40-916c-babd94757014" />



# Step 6 – Display Student Data

After submission, the application retrieves the stored data from the database and displays it on the web page.

Student List Screenshot

<img width="1916" height="959" alt="student data fill" src="https://github.com/user-attachments/assets/27d65a7f-8113-41ea-9232-13fd0215f099" />

Step 7 – Amazon RDS Database Setup

Amazon RDS MySQL database was created to store the student data.

Configuration:

Engine: MySQL
Instance Type: db.t3.micro
Database Name: studentdb

RDS Database Screenshot:
<img width="1920" height="1020" alt="rds database" src="https://github.com/user-attachments/assets/239e83fb-223b-4152-836e-1934147f2bae" />

# Step 8 – Database Creation

The database and student table were created using SQL.

SQL Commands
CREATE DATABASE studentdb;

USE studentdb;

CREATE TABLE students (
id INT AUTO_INCREMENT PRIMARY KEY,
name VARCHAR(100),
email VARCHAR(100),
course VARCHAR(100),
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
Database Creation Screenshot
<img width="1625" height="967" alt="Screenshot 2026-03-12 152633" src="https://github.com/user-attachments/assets/8c43e763-973b-4439-9c32-9c8888e2fc9d" />

# Step 9 – Database Records

After submitting the form, the records are successfully stored in the MySQL database.

MySQL Table Screenshot:

<img width="1495" height="963" alt="mysdl db" src="https://github.com/user-attachments/assets/ed100e64-2e2e-4cae-834a-ec706ebffde0" />

# Step 10 – MySQL Connector Setup

The MySQL JDBC connector was added to the Tomcat library directory to enable database connectivity.

Path:

/opt/tomcat/lib/
MySQL Connector Screenshot:

<img width="1920" height="1020" alt="jar file" src="https://github.com/user-attachments/assets/89d4700e-8e23-4eff-b715-c9a39ba5c5b7" />

# Step 11 – Reverse Proxy Configuration

Nginx was installed on the proxy server to forward traffic to the backend server.

Install nginx:

sudo yum install nginx -y

Configure reverse proxy:

location /student {
proxy_pass http://BACKEND_PRIVATE_IP:8080/student;
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
}

Restart nginx:

sudo systemctl restart nginx
Application Access

Users access the application through the proxy server:

http://13.208.241.151/student

Direct access to the backend server is blocked.

Security Implementation

Security improvements implemented:

Backend server not publicly accessible

Reverse proxy controls incoming traffic

Security groups restrict direct access

Database secured inside AWS network

# Conclusion

This project demonstrates secure deployment of a Java web application using a reverse proxy architecture on AWS.

By using Nginx as a reverse proxy, the backend application server remains protected while still allowing users to access the application through the proxy server.

This architecture is widely used in real-world production environments for improved security and scalability.


