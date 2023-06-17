# Install-Jenkins

###Commands to install Jenkins on amazon linux (Amazon Linux 2) ec2 instance:

Create Amazon Ec2 instance
===Upgrade the OS====
sudo yum upgrade -y

===Install Git=======
sudo yum install git -y


===Install Java=====
sudo amazon-linux-extras install java-openjdk11 -y
java --version
which java
readlink -f /usr/bin/java

======Intall Maven=====
yum install maven -y

===Install Maven====
cd /opt
sudo wget https://dlcdn.apache.org/maven/maven-3/3.9.2/binaries/apache-maven-3.9.2-bin.tar.gz
sudo tar xvf apache-maven-3.9.2-bin.tar.gz
ls -ltr
cd
echo "export M2_HOME=/opt/apache-maven-3.9.2" >> .bash_profile
echo "export M2=$M2_HOME/bin" >> .bash_profile
echo "export PATH=$M2:$PATH" >> .bash_profile
source ~/.bash_profile
echo $M2_HOME 


====Install Jenkins=====
wget -O /etc/yum.repos.d/jenkins.repo \https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum upgrade
sudo yum install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
ps -ef | grep jenkins

====Jenkins Password======
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

====How to Access jenkins==
http://publicip:8080/

