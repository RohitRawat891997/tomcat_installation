# Apache Tomcat Installation and Configuration

This repository provides step-by-step instructions to install and configure Apache Tomcat 10 on a CentOS/RHEL-based system using Java 11. The configuration includes setting up the Tomcat server, user roles, systemd service, firewall, and SELinux policies for secure operation.

## Prerequisites

- A CentOS/RHEL-based system with root privileges.
- Java 11 Development Environment.

---

## Installation Steps

### 1. Update and Install Dependencies
- **Update system packages:**

  sudo dnf update -y

Install Java 11 Development Environment:
bash
Copy
Edit
sudo dnf install java-11-openjdk-devel -y
Verify the Java installation:
bash
Copy
Edit
java -version
2. Download and Extract Apache Tomcat
Change directory to /opt:
bash
Copy
Edit
cd /opt
Download Apache Tomcat 10.1.34:
bash
Copy
Edit
wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.34/bin/apache-tomcat-10.1.34.tar.gz
Extract the Tomcat archive:
bash
Copy
Edit
tar -xzvf apache-tomcat-10.1.34.tar.gz
Rename the extracted folder:
bash
Copy
Edit
mv apache-tomcat-10.1.34 tomcat
3. Set CATALINA_HOME Environment Variable
Set the environment variable:
bash
Copy
Edit
echo 'export CATALINA_HOME=/opt/tomcat' | sudo tee -a /etc/profile.d/tomcat.sh
Source the environment variable:
bash
Copy
Edit
source /etc/profile.d/tomcat.sh
4. Create Tomcat User
Create a dedicated tomcat user:
bash
Copy
Edit
sudo useradd -r -m -U -d /opt/tomcat -s /bin/false tomcat
Change ownership of the Tomcat directory:
bash
Copy
Edit
sudo chown -R tomcat: /opt/tomcat
5. Create and Configure Systemd Service
Create and edit the systemd service file:
bash
Copy
Edit
sudo vim /etc/systemd/system/tomcat.service
Add the following service configuration:
ini
Copy
Edit
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking
Environment=JAVA_HOME=/usr/lib/jvm/jre-openjdk
Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'
ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh
User=tomcat
Group=tomcat
UMask=0007
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
Save and exit (:wq!).
6. Start and Enable Tomcat Service
Reload systemd to apply the new service:
bash
Copy
Edit
sudo systemctl daemon-reload
Start Tomcat:
bash
Copy
Edit
sudo systemctl start tomcat
Enable Tomcat to start on boot:
bash
Copy
Edit
sudo systemctl enable tomcat
Check Tomcat service status:
bash
Copy
Edit
sudo systemctl status tomcat
7. Configure Firewall
Open port 8080 for Tomcat:
bash
Copy
Edit
sudo firewall-cmd --permanent --zone=public --add-port=8080/tcp
Reload the firewall:
bash
Copy
Edit
sudo firewall-cmd --reload
8. Configure Tomcat Manager User Roles
Edit tomcat-users.xml to add manager and admin roles:
bash
Copy
Edit
sudo vim /opt/tomcat/conf/tomcat-users.xml
Add the following lines:
xml
Copy
Edit
<role rolename="manager-gui"/>
<role rolename="admin-gui"/>
<user username="admin" password="your_password" roles="manager-gui,admin-gui"/>
Save and exit.
9. Restrict Tomcat Manager Access
Edit context.xml to restrict access to Tomcat Manager:
bash
Copy
Edit
sudo vim /opt/tomcat/webapps/manager/META-INF/context.xml
Modify or comment out the RemoteAddrValve settings:
xml
Copy
Edit
<Valve className="org.apache.catalina.valves.RemoteAddrValve"
      allow="192\.168\.1\.10" />   <!-- Use your IP or allow all with allow=".*" -->
Save and exit.
10. Restart Tomcat Service
Restart the Tomcat service to apply changes:
bash
Copy
Edit
sudo systemctl restart tomcat
11. Adjust SELinux Policies
Temporarily disable SELinux enforcing mode:
bash
Copy
Edit
sudo setenforce 0
Allow HTTPD to make network connections:
bash
Copy
Edit
sudo setsebool -P httpd_can_network_connect 1
Re-enable SELinux enforcing mode:
bash
Copy
Edit
sudo setenforce 1
License
This project is licensed under the MIT License. See the LICENSE file for details.

vbnet
Copy
Edit

This `README.md` should provide clear instructions for users to follow when setting up Apache Tomcat 

