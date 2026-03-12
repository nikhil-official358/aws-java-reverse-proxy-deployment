Java Application Deployment with Reverse Proxy on AWS
Project Overview

This project demonstrates how to securely deploy a Java-based Student Registration Web Application on Amazon Web Services (AWS) using a Reverse Proxy Architecture.

In many legacy systems, backend application servers are directly exposed to the internet, which creates serious security risks. To solve this problem, this project implements a reverse proxy architecture, where the backend application server is hidden from public access and all user requests pass through a proxy server.

The reverse proxy forwards incoming HTTP requests to the backend application server while preventing direct access to the backend instance.

This architecture improves:

Security

Scalability

Traffic control

Infrastructure management

The application allows users to register student information through a web form, and the submitted data is stored in an Amazon RDS MySQL database.

Architecture
User Browser
     │
     ▼
Reverse Proxy Server (Nginx)
     │
     ▼
Backend Application Server (Apache Tomcat)
     │
     ▼
Amazon RDS MySQL Database
Traffic Flow

The user sends a request from the browser.

The request reaches the Reverse Proxy Server (Nginx).

Nginx forwards the request to the Backend Tomcat Server.

The Java application processes the request.

Student registration data is stored in Amazon RDS MySQL database.

The response is returned back to the user through the proxy.

This architecture ensures the backend server is not directly exposed to the internet.

AWS Services Used
Amazon EC2

Two EC2 instances are used:

Backend Server

Runs Apache Tomcat

Hosts the Java .war application

Accessible only internally

Reverse Proxy Server

Runs Nginx

Receives public HTTP requests

Forwards requests to backend server

Amazon RDS

Amazon RDS is used to host a MySQL database for storing student registration data.

Features used:

Managed database service

Secure networking

Reliable data storage

Security Groups

Security groups are configured to control traffic.

Proxy Security Group

Allows HTTP (Port 80) from internet

Backend Security Group

Allows Port 8080 only from Proxy server

This ensures direct backend access is blocked.

Technologies Used

Java

Apache Tomcat

Nginx Reverse Proxy

MySQL

Amazon Web Services (AWS)

Application Access

The application can be accessed through the reverse proxy using the following URL:

http://<Proxy-Public-IP>/student

Direct access to the backend server is restricted for security reasons.

Infrastructure Setup
Step 1 – Launch Backend EC2 Instance

Create an EC2 instance that will host the Java application.

Configuration:

Name:

java-backend-server

Operating System:

Amazon Linux 2

Instance Type:

t2.micro

Security Group Rules:

Type	Port	Source
SSH	22	My IP
Custom TCP	8080	Proxy Server
Step 2 – Install Java

Connect to the backend instance and install Java.

sudo yum update -y
sudo yum install java-11-amazon-corretto -y

Verify installation:

java -version
Step 3 – Install Apache Tomcat

Download Apache Tomcat and extract it.

cd /opt
sudo wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.89/bin/apache-tomcat-9.0.89.tar.gz
sudo tar -xvzf apache-tomcat-9.0.89.tar.gz
sudo mv apache-tomcat-9.0.89 tomcat

Start Tomcat server:

sudo /opt/tomcat/bin/startup.sh

Test Tomcat:

http://BACKEND-IP:8080
Step 4 – Deploy Java Application

Upload the student.war file to the Tomcat webapps directory.

/opt/tomcat/webapps/

Once uploaded, Tomcat automatically deploys the application.

Test the application:

http://BACKEND-IP:8080/student
Database Setup
Step 5 – Create Amazon RDS Database

Navigate to:

AWS Console → RDS → Create Database

Configuration:

Engine:

MySQL

Instance Type:

db.t3.micro

Database Name:

studentdb
Step 6 – Create Database Table

Connect to the database and create the student table.

CREATE DATABASE studentdb;

USE studentdb;

CREATE TABLE students (
id INT AUTO_INCREMENT PRIMARY KEY,
name VARCHAR(100),
email VARCHAR(100),
course VARCHAR(100),
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
Database Connector Setup
Step 7 – Install MySQL Connector

Download MySQL connector for Java and place it in the Tomcat lib directory.

Location:

/opt/tomcat/lib/

Restart Tomcat after placing the connector.

Reverse Proxy Setup
Step 8 – Launch Reverse Proxy EC2 Instance

Create a second EC2 instance.

Name:

reverse-proxy-server

Security Group:

Type	Port	Source
SSH	22	My IP
HTTP	80	0.0.0.0/0
Step 9 – Install Nginx
sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
Step 10 – Configure Reverse Proxy

Edit Nginx configuration file.

sudo nano /etc/nginx/nginx.conf

Add the following configuration:

location /student {
proxy_pass http://BACKEND_PRIVATE_IP:8080/student;
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
}

Restart Nginx:

sudo systemctl restart nginx
Security Implementation

To improve security:

Backend server is not publicly accessible

Only proxy server can access port 8080

Security groups restrict traffic flow

This ensures secure communication between components.

Features

Reverse proxy architecture

Secure backend server

Java web application deployment

AWS cloud infrastructure

Database integration

Controlled traffic routing

Project Outcome

After completing the deployment:

Users can access the application through the proxy server.

Student registration data is successfully stored in the database.

Backend server remains secure and inaccessible from the internet.

Application URL:

http://<Proxy-Public-IP>/student
Conclusion

This project demonstrates how to deploy a secure cloud-based web application using AWS infrastructure.

By implementing a reverse proxy architecture, the backend application server is protected from direct exposure, improving overall system security.

This setup is commonly used in real-world production environments for hosting scalable and secure applications.
