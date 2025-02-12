# 🚀 VProfile Project

---

## 📖 Overview
VProfile is a multi-tier web application designed to demonstrate infrastructure automation using Vagrant and VirtualBox. It includes various services such as a web server, application server, message broker, caching service, search indexing, and a database. The project automates provisioning using Vagrant and sets up a complete working environment for deployment.

---

## 📑 Table of Contents
1. Prerequisites
2. Architecture
3. Workflow
4. Task Execution
5. Cleaning Up Resources
6. Conclusion
7. Instructor

---

## 🔑 Prerequisites
Before you start, ensure you have the following:
1. **Oracle VM VirtualBox** - For virtualization
2. **Vagrant** - To automate VM setup
3. **Vagrant Plugins** - Required for network and provisioning

---

## 🗺️ Architecture

### 🔄 Workflow:
The provisioning process follows this sequence:

1. **Nginx** → Web Service
2. **Tomcat** → Application Server
3. **RabbitMQ** → Broker/Queuing Agent
4. **Memcache** → DB Caching
5. **ElasticSearch** → Indexing/Search Service
6. **MySQL** → SQL Database

**Setup Order:**
1. **MySQL** (Database Service)
2. **Memcache** (DB Caching Service)
3. **RabbitMQ** (Broker/Queue Service)
4. **Tomcat** (Application Service)
5. **Nginx** (Web Service)

---

Here’s the refined **VProfile Project** setup with all six provisioning steps before Memcache, structured in a clean and readable format:  

---

## 📝 Task 1: VM Setup  
1. Clone the source code:  
   ```bash
   $ git clone https://github.com/<your-repo>/vprofile-project.git
   ```
2. Navigate into the repository:  
   ```bash
   $ cd vprofile-project
   ```
3. Switch to the local branch:  
   ```bash
   $ git checkout <branch-name>
   ```
4. Navigate into the **vagrant/Manual_provisioning** directory:  
   ```bash
   $ cd vagrant/Manual_provisioning
   ```
5. Start the VMs using Vagrant:  
   ```bash
   $ vagrant up
   ```
6. Verify running VMs:  
   ```bash
   $ vagrant status
   ```

---

## 📝 Task 2: MySQL Setup  
1. Login to the MySQL VM:  
   ```bash
   $ vagrant ssh db01
   ```
2. Verify host entries:  
   ```bash
   # cat /etc/hosts
   ```
3. Update OS with the latest patches:  
   ```bash
   # sudo dnf update -y
   ```
4. Install MySQL server:  
   ```bash
   # sudo dnf install mysql-server -y
   ```
5. Start and enable MySQL service:  
   ```bash
   # sudo systemctl start mysqld
   # sudo systemctl enable mysqld
   # sudo systemctl status mysqld
   ```
6. Secure MySQL installation:  
   ```bash
   # mysql_secure_installation
   ```

---

## 📝 Task 3: RabbitMQ Setup  
1. Login to the RabbitMQ VM:  
   ```bash
   $ vagrant ssh rmq01
   ```
2. Verify host entries:  
   ```bash
   # cat /etc/hosts
   ```
3. Update OS with the latest patches:  
   ```bash
   # sudo dnf update -y
   ```
4. Install RabbitMQ server:  
   ```bash
   # sudo dnf install epel-release -y
   # sudo dnf install rabbitmq-server -y
   ```
5. Start and enable RabbitMQ service:  
   ```bash
   # sudo systemctl start rabbitmq-server
   # sudo systemctl enable rabbitmq-server
   # sudo systemctl status rabbitmq-server
   ```
6. Enable RabbitMQ management plugin:  
   ```bash
   # sudo rabbitmq-plugins enable rabbitmq_management
   ```

---

## 📝 Task 4: Memcache Setup  
1. Login to the Memcache VM:  
   ```bash
   $ vagrant ssh mc01
   ```
2. Verify host entries:  
   ```bash
   # cat /etc/hosts
   ```
3. Update OS with the latest patches:  
   ```bash
   # sudo dnf update -y
   ```
4. Install Memcached:  
   ```bash
   # sudo dnf install epel-release -y
   # sudo dnf install memcached -y
   ```
5. Start and enable Memcached service:  
   ```bash
   # sudo systemctl start memcached
   # sudo systemctl enable memcached
   # sudo systemctl status memcached
   ```
6. Allow remote connections:  
   ```bash
   # sed -i 's/127.0.0.1/0.0.0.0/g' /etc/sysconfig/memcached
   # sudo systemctl restart memcached
   ```

---

Let me know if you need more refinements! 🚀

## 🗑️ Cleaning Up Resources
To remove all virtual machines and clean up resources, execute:
```bash
$ vagrant destroy --force
```

---

## ✅ Conclusion
In this project, we successfully set up a **multi-tier web application** using **Vagrant and VirtualBox**, provisioning various services like MySQL, Memcache, RabbitMQ, Tomcat, and Nginx. The architecture supports scalability and ensures efficient load management.

---

## 👨‍🏫 Instructor
This project was completed under the mentorship of **Imran Teli**.

