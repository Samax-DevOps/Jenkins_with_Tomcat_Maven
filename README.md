# tomcat-project

***********************Install Apache-TOMCAT************************************** 
sudo apt update 
sudo apt install openjdk-11-jdk
java -version
wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.28/bin/apache-tomcat-10.1.28.tar.gz         (get latest tar.gz from https://tomcat.apache.org/download)

sudo mkdir -p /opt/tomcat
sudo tar xzvf apache-tomcat-*tar.gz -C /opt/tomcat --strip-components=1

*******************Create A Dedicated User****************************************
sudo groupadd tomcat
sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat

*****************User Permissions***************************************
sudo chown -R tomcat: /opt/tomcat
sudo sh -c 'chmod +x /opt/tomcat/bin/*.sh'


************Create A Systemd Service File For TOMCAT*********************
**************Check Java version************
sudo update-alternatives --config java
*****+ /usr/lib/jvm/java-11-openjdk-amd64/bin/java 

sudo nano /etc/systemd/system/tomcat.service
//////---tomcat.service file---//////




[Unit]
Description=Tomcat Web Servlet Container
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

# Restart settings
RestartSec=10
Restart=always

# Environment variables
Environment="JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64"
Environment="JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom"

Environment="CATALINA_BASE=/opt/tomcat"
Environment="CATALINA_HOME=/opt/tomcat"
Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

# Use catalina.sh for starting and stopping Tomcat
ExecStart=/opt/tomcat/bin/catalina.sh start
ExecStop=/opt/tomcat/bin/catalina.sh stop

[Install]
WantedBy=multi-user.target




sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl enable tomcat
sudo systemctl status tomcat


**************Add Roles and Admin Username and Password**************************** 
sudo nano /opt/tomcat/conf/tomcat-users.xml
<role rolename="admin-gui,manager-gui,manager-script,manager-jmx,manager-status,admin-gui"/>
<user username="admin" password="admin" roles="admin-gui,manager-gui,manager-script"/>

**************Enable TOMCAT and Host Manager Remote Access****************************
*************deployment error so we have to commit these two lines*****
sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml

<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve"
allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
Comment this line 

sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml

<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve"
allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
Comment this line 

sudo systemctl restart tomcat

***************Change TOMCAT Port to 8000*****************************
sudo su 
cd /opt/tomcat/conf
sudo nano server.xml





****************Install JENKINS***************************************
sudo apt update
sudo apt install openjdk-17-jre  (For Jenkins)
java -version

curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
/usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt-get install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins

***************Not Required*************************************** 
firewall-cmd —zone=public --permanent —add-port=8080/tcp firewall-cmd --reload
***************Skip this step*************************************

*****************Change jenkins port to 8081************** 
sudo nano /etc/sysconfig/jenkins  ( look for port 8080 and change to 8081)
cat /etc/sysconfig/jenkins |grep JENKINS_PO

systemctl restart jenkins
systemctl start jenkins
systemctl status jenkins

******************************Password from Jenkins*********************
Cat /var/lib/jenkins/secrets/initialAdminPassword  ( copy the password in this admin and create account)


*******************Install Maven**************************************
sudo apt install maven
mvn --version





****************************Global Tool Configuration********
JDK| JDK-18= /usr/lib/jvm/java-11-openjdk-amd64
GIT| /bin/git (which git)
MVN| 3.9.4

****************************Creating Job************
Source code management 
GIT| Address for the repo ( from Git) 
Credential ( your git hub credentials)
Branch| */master
**********************BUILD************
Invoke Top-level Maven Targets
Maven Version | mvm-394
Goal | Clean install 
************************Post build Actio****************
WAR/EAR file | **/*.war
Context path | / 
Container | Tomcat 9.x Remote
Credentials | deployer | deployer 
Tomcat URL | URL IP with Port 8080



