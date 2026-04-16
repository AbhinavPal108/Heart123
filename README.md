The steps involved in SonarQube installation, SonarScanner CLI installation, configuration, and setting up as a system service are given below.

1) Basic Project Setup
Open IntelliJ IDEA and create a Java Maven project.
Add required Sonar plugin dependency in pom.xml.
Write Java source code.
Push the project to GitHub repository using token authentication.
Use Jenkins + GitHub + SonarQube integration for CI/CD quality analysis.
Requirements
Jenkins: http://localhost:8080
SonarQube: http://localhost:9000
GitHub repository
2) Install PostgreSQL

Install PostgreSQL database:

sudo apt update
sudo apt install -y postgresql-common postgresql

Enable and start service:

sudo systemctl enable postgresql
sudo systemctl start postgresql

Login to PostgreSQL:

sudo -u postgres psql
3) Create Database and User

Create SonarQube database and user:

CREATE ROLE sonaruser WITH LOGIN ENCRYPTED PASSWORD 'your_password';
CREATE DATABASE sonarqube;
GRANT ALL PRIVILEGES ON DATABASE sonarqube TO sonaruser;
\c sonarqube
GRANT ALL PRIVILEGES ON SCHEMA public TO sonaruser;
\q
4) Install OpenJDK

SonarQube requires Java 17.

sudo apt install openjdk-17-jdk
java -version
5) Download and Setup SonarQube

Download SonarQube:

wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube.zip
unzip sonarqube.zip
sudo mv sonarqube /opt/sonarqube

Create system user:

sudo adduser --system --no-create-home --group --disabled-login sonarqube
sudo chown -R sonarqube:sonarqube /opt/sonarqube
6) Installation of SonarScanner CLI

Download and install SonarScanner:

unzip sonar-scanner-cli.zip
sudo mv sonar-scanner /opt/sonarscanner

Edit scanner configuration:

sudo nano /opt/sonarscanner/conf/sonar-scanner.properties

Add:

sonar.host.url=http://127.0.0.1:9000

Create command link:

sudo ln -s /opt/sonarscanner/bin/sonar-scanner /usr/local/bin/sonar-scanner

Verify:

sonar-scanner -v
7) Configure SonarQube

Edit SonarQube configuration:

sudo nano /opt/sonarqube/conf/sonar.properties

Add:

sonar.jdbc.username=sonaruser
sonar.jdbc.password=your_password
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube
sonar.web.port=9000

System tuning:

sudo nano /etc/sysctl.conf

Add:

vm.max_map_count=524288
fs.file-max=131072

Reload firewall:

sudo ufw allow 9000/tcp
sudo ufw reload
8) Setup SonarQube as a System Service

Create service file:

sudo nano /etc/systemd/system/sonarqube.service

Add:

[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
User=sonarqube
Group=sonarqube
Restart=always

[Install]
WantedBy=multi-user.target

Start service:

sudo systemctl daemon-reload
sudo systemctl enable sonarqube
sudo systemctl start sonarqube
sudo systemctl status sonarqube
9) Jenkins Integration Steps
Open Jenkins.
Create New Item → Maven Project.
Configure GitHub repository URL.
Add build goals (clean install sonar:sonar).
Add SonarQube server URL and token.
Build project.
SonarQube inspects the code and generates quality report, bugs, vulnerabilities, and code smells.
10) Output

Open browser:

http://localhost:9000

Default credentials:

Username: admin
Password: admin

The dashboard displays:

Code quality
Bugs
Vulnerabilities
Duplications
Test coverage
Maintainability report
Conclusion

Thus, the steps involved in installing SonarQube, installing SonarScanner CLI, configuring SonarQube, integrating with Jenkins, and setting up SonarQube as a Linux system service were successfully completed.

Thank You
