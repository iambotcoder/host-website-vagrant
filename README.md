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

<p align="center">
  ![Screenshot 2025-02-12 224814](https://github.com/user-attachments/assets/161946d6-ee1e-4c89-9386-93af0a20d9a9)
</p>

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

### 📜 **Vagrantfile**
Create a file named **Vagrantfile** and add the following code:

```ruby
Vagrant.configure("2") do |config|
  config.hostmanager.enabled = true 
  config.hostmanager.manage_host = true

  ### DB VM ###
  config.vm.define "db01" do |db01|
    db01.vm.box = "eurolinux-vagrant/centos-stream-9"
    #db01.vm.box_version = "9.0.48"  # Commented to avoid installation errors
    db01.vm.hostname = "db01"

    # Port Forwarding
    db01.vm.network "forwarded_port", guest: 22, host: 2222
    db01.vm.network "private_network", ip: "192.168.56.15"
    
    db01.vm.provider "virtualbox" do |vb|
      vb.memory = "600"
    end
  end

  ### Memcache VM ###
  config.vm.define "mc01" do |mc01|
    mc01.vm.box = "eurolinux-vagrant/centos-stream-9"
    #mc01.vm.box_version = "9.0.48"
    mc01.vm.hostname = "mc01"

    # Port Forwarding
    mc01.vm.network "forwarded_port", guest: 22, host: 2207
    mc01.vm.network "private_network", ip: "192.168.56.14"
    
    mc01.vm.provider "virtualbox" do |vb|
      vb.memory = "600"
    end
  end

  ### RabbitMQ VM ###
  config.vm.define "rmq01" do |rmq01|
    rmq01.vm.box = "eurolinux-vagrant/centos-stream-9"
    #rmq01.vm.box_version = "9.0.48"
    rmq01.vm.hostname = "rmq01"

    # Port Forwarding
    rmq01.vm.network "forwarded_port", guest: 22, host: 2204
    rmq01.vm.network "private_network", ip: "192.168.56.13"

    rmq01.vm.provider "virtualbox" do |vb|
      vb.memory = "600"
    end
  end

  ### Tomcat VM ###
  config.vm.define "app01" do |app01|
    app01.vm.box = "eurolinux-vagrant/centos-stream-9"
    #app01.vm.box_version = "9.0.48"
    app01.vm.hostname = "app01"

    # Port Forwarding
    app01.vm.network "forwarded_port", guest: 22, host: 2205
    app01.vm.network "private_network", ip: "192.168.56.12"

    app01.vm.provider "virtualbox" do |vb|
      vb.memory = "800"
    end
  end

  ### Nginx VM ###
  config.vm.define "web01" do |web01|
    web01.vm.box = "ubuntu/jammy64"
    web01.vm.hostname = "web01"

    # Port Forwarding
    web01.vm.network "forwarded_port", guest: 22, host: 2206
    web01.vm.network "private_network", ip: "192.168.56.11"

    web01.vm.provider "virtualbox" do |vb|
      # First provision with GUI enabled, then disable it after setup
      # vb.gui = true  
      vb.memory = "800"
    end
  end
end
```

---


## 🚀 Setup Instructions

#### 1️⃣ Install Dependencies

Ensure the following are installed:

- [Vagrant](https://www.vagrantup.com/)
- [VirtualBox](https://www.virtualbox.org/)

#### 2️⃣ Clone the Repository

```sh
git clone https://github.com/hkhcoder/vprofile-project.git
cd vagrant-setup
```

#### 3️⃣ Provision the Virtual Machines

Run the following command to start all VMs:
```sh
vagrant up
```

#### 4️⃣ SSH into a Virtual Machine

To access a specific VM, use:
```sh
vagrant ssh <vm-name>
```
For example:
```sh
vagrant ssh web01
```

#### 5️⃣ Managing VMs

- **Stop all VMs:** `vagrant halt`
- **Restart all VMs:** `vagrant reload`
- **Destroy all VMs:** `vagrant destroy`
- **List running VMs:** `vagrant status`

### 📝 Notes

- **Nginx VM (`web01`) requires manual network configuration**:
  1. **First Boot** → Enable `vb.gui = true` in `Vagrantfile`
  2. Set up the network in VirtualBox:
     - **Adapter 1:** NAT
     - **Adapter 2:** Bridged Adapter
  3. Once installed, disable the GUI (`vb.gui = false`) and run `vagrant reload`.



---

### **1. MySQL Setup**  

1. Login to the database VM:  
   ```bash
   vagrant ssh db01
   ```
   
2. Verify and update `/etc/hosts` if necessary: 
    
   ```bash
   cat /etc/hosts
   ```
   
3. Update OS with latest patches:  
   
   ```bash
   dnf update -y
   ```
   
4. Set repository:
     
   ```bash
   dnf install epel-release -y
   ```
   
5. Install MariaDB package:  
   
   ```bash
   dnf install git mariadb-server -y
   ```
   
6. Start and enable MariaDB service: 
    
   ```bash
   systemctl start mariadb
   systemctl enable mariadb
   ```
   
7. Run MySQL secure installation script:
     
   ```bash
   mysql_secure_installation
   ```
   
   - Set root password (`admin123`)  
   - Remove anonymous users  
   - Disallow root login remotely (No)  
   - Remove test database  
   - Reload privilege tables
    
8. Create database and user:
     
   ```bash
   mysql -u root -padmin123
   ```
   
   ```sql
   mysql> CREATE DATABASE accounts;
   mysql> GRANT ALL PRIVILEGES ON accounts.* TO 'admin'@'localhost' IDENTIFIED BY 'admin123';
   mysql> GRANT ALL PRIVILEGES ON accounts.* TO 'admin'@'%' IDENTIFIED BY 'admin123';
   mysql> FLUSH PRIVILEGES;
   mysql> exit;
   ```
   
9. Download source code and initialize the database:  
   
   ```bash
   cd /tmp/
   git clone -b local https://github.com/hkhcoder/vprofile-project.git
   cd vprofile-project
   mysql -u root -padmin123 accounts < src/main/resources/db_backup.sql
   mysql -u root -padmin123 accounts
   ```
   
   ```sql
   mysql> SHOW TABLES;
   mysql> exit;
   ```
   
10. Restart MariaDB:  
   
    ```bash
    systemctl restart mariadb
    ```

---

### **2. Memcache Setup**  

1. Login to the Memcache VM:  
   
   ```bash
   vagrant ssh mc01
   ```
   
2. Verify and update `/etc/hosts` if necessary:  
   
   ```bash
   cat /etc/hosts
   ```
   
3. Update OS with latest patches:  
   
   ```bash
   dnf update -y
   ```
   
4. Install Memcache:  
   
   ```bash
   sudo dnf install epel-release -y
   sudo dnf install memcached -y
   ```
   
5. Start and enable Memcache service:  
   
   ```bash
   sudo systemctl start memcached
   sudo systemctl enable memcached
   sudo systemctl status memcached
   ```
6. Allow external connections:
     
   ```bash
   sed -i 's/127.0.0.1/0.0.0.0/g' /etc/sysconfig/memcached
   ```
   
7. Restart Memcached:  
   
   ```bash
   sudo systemctl restart memcached
   ```

---

### **3. RabbitMQ Setup**  

1. Login to the RabbitMQ VM:  
   
   ```bash
   vagrant ssh rmq01
   ```
   
2. Verify and update `/etc/hosts` if necessary:  
   
   ```bash
   cat /etc/hosts
   ```
   
3. Update OS with latest patches:  
   
   ```bash
   dnf update -y
   ```
   
4. Install EPEL repository:  
   
   ```bash
   dnf install epel-release -y
   ```
   
5. Install dependencies:  
   
   ```bash
   sudo dnf install wget -y
   dnf -y install centos-release-rabbitmq-38
   dnf --enablerepo=centos-rabbitmq-38 -y install rabbitmq-server
   systemctl enable --now rabbitmq-server
   ```
   
6. Configure RabbitMQ user:  
   
   ```bash
   sudo sh -c 'echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config'
   sudo rabbitmqctl add_user test test
   sudo rabbitmqctl set_user_tags test administrator
   sudo rabbitmqctl set_permissions -p / test ".*" ".*" ".*"
   ```
   
7. Restart RabbitMQ service:  
   
   ```bash
   sudo systemctl restart rabbitmq-server
   ```

---

### **4. Tomcat Setup**

1. Login to the Tomcat VM:  
   
   ```bash
   vagrant ssh app01
   ```
   
2. Verify and update `/etc/hosts` if necessary:  
   
   ```bash
   cat /etc/hosts
   ```
   
3. Update OS with latest patches:  
   
   ```bash
   dnf update -y
   ```
   
4. Set repository:  
   
   ```bash
   dnf install epel-release -y
   ```
   
5. Install dependencies:  
   
   ```bash
   dnf -y install java-17-openjdk java-17-openjdk-devel
   dnf install git wget -y
   ```
   
6. Change directory to `/tmp`:  
   
   ```bash
   cd /tmp/
   ```
   
7. Download and extract Tomcat:  
   
   ```bash
   wget https://archive.apache.org/dist/tomcat/tomcat-10/v10.1.26/bin/apache-tomcat-10.1.26.tar.gz
   tar xzvf apache-tomcat-10.1.26.tar.gz
   ```
   
8. Create Tomcat user:  
   
   ```bash
   useradd --home-dir /usr/local/tomcat --shell /sbin/nologin tomcat
   ```
   
9. Copy Tomcat files to home directory:  
   
   ```bash
   cp -r /tmp/apache-tomcat-10.1.26/* /usr/local/tomcat/
   chown -R tomcat.tomcat /usr/local/tomcat
   ```
   
## Setup systemctl command for tomcat   

10. Create Tomcat service file:  
   
    ```bash
    vi /etc/systemd/system/tomcat.service
    ```
   
    - Add the provided service configuration
      
    ```ruby
    
     [Unit]
     Description=Tomcat
     After=network.target
    
     [Service]
     User=tomcat
     Group=tomcat
     WorkingDirectory=/usr/local/tomcat
     Environment=JAVA_HOME=/usr/lib/jvm/jre
     Environment=CATALINA_PID=/var/tomcat/%i/run/tomcat.pid
     Environment=CATALINA_HOME=/usr/local/tomcat
     Environment=CATALINE_BASE=/usr/local/tomcat
     ExecStart=/usr/local/tomcat/bin/catalina.sh run
     ExecStop=/usr/local/tomcat/bin/shutdown.sh
     RestartSec=10
     Restart=always
    
     [Install]
     WantedBy=multi-user.target
    ```
   
11. Reload systemd and enable Tomcat:  
    
   ```bash
    systemctl daemon-reload
    systemctl start tomcat
    systemctl enable tomcat
   ```
   
12. Configure firewall:  
    
   ```bash
    systemctl start firewalld
    systemctl enable firewalld
    firewall-cmd --zone=public --add-port=8080/tcp --permanent
    firewall-cmd --reload
   ```

#### **Code Build & Deployment**

1. Install Maven:  
   
   ```bash
   cd /tmp/
   wget https://archive.apache.org/dist/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.zip
   unzip apache-maven-3.9.9-bin.zip
   cp -r apache-maven-3.9.9 /usr/local/maven3.9
   export MAVEN_OPTS="-Xmx512m"
   ```
   
2. Download source code:  
   
   ```bash
   git clone -b local https://github.com/hkhcoder/vprofile-project.git
   ```
   
3. Update configuration:  
   
   ```bash
   cd vprofile-project
   vim src/main/resources/application.properties
   ```
   
4. Build the code:  
   
   ```bash
   /usr/local/maven3.9/bin/mvn install
   ```
   
5. Deploy artifact:  
   
   ```bash
   systemctl stop tomcat
   rm -rf /usr/local/tomcat/webapps/ROOT*
   cp target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
   systemctl start tomcat
   chown tomcat.tomcat /usr/local/tomcat/webapps -R
   systemctl restart tomcat
   ```

---

### **5. Nginx Setup**

1. Login to the Nginx VM:  
   
   ```bash
   vagrant ssh web01
   sudo -i
   ```
   
2. Verify and update `/etc/hosts` if necessary:  
   
   ```bash
   cat /etc/hosts
   ```
   
3. Update OS with latest patches:  
   
   ```bash
   apt update
   apt upgrade
   ```
   
4. Install Nginx:  
   
   ```bash
   apt install nginx -y
   ```
   
5. Create Nginx configuration file:  
   
   ```bash
   vi /etc/nginx/sites-available/vproapp
   ```
   
   - Add the provided configuration

   ```ruby
   
   upstream vproapp {
     server app01:8080;
     }
     server {
       listen 80;
       location / {
         proxy_pass http://vproapp;
       }
   }
 
   ```
   
7. Activate Nginx site:  
   
   ```bash
   rm -rf /etc/nginx/sites-enabled/default
   ln -s /etc/nginx/sites-available/vproapp /etc/nginx/sites-enabled/vproapp
   ```
   
8. Restart Nginx:  
   
   ```bash
   systemctl restart nginx
   ```

---



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

