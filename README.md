# Install-Jenkins

##Commands to install Jenkins on amazon linux (Amazon Linux 2) ec2 instance:

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

=======Configure Java==========
echo "export JAVA_HOME=$(readlink -f /usr/bin/java | sed 's:/bin/java$::')" >> ~/.bashrc
source ~/.bashrc
nano ~/.bashrc
export PATH=$PATH:$JAVA_HOME/bin
source ~/.bashrc



==========================================================
1) Jenkins installation on ec2 instance
2) Adding Jenkins user to visudofile
3) Global tool config:
  manage jenkins -->Global tool config--> git, mvn, java, docker
4) manage plugins
5) Jenkins CLI:
   Home dir: /var/lib/jenkins
   log dir: /var/log/jenkins/jenkins.log
   how to restart jenkins: service jenkins restart
6) Jenkins Job:
   freestyle
   maven project
   pipeline
   folder
   multibranch
7) Trigger in jenkins job:
   -build periodically cron tab setting (*/1 * * * *)
   -poll scm (if there is any change in source code)
   -github-webhook for gitscm polling

extension of jenkins: .jpi


=============================================================
Troubleshooting jenkins:
1)check jenkins process:
  #ps -ef | grep jenkins: (we get pid and port number)
2) Check logs: based on logs
  #tail -f /var/log/jenkins/jenkins.log
3)Check security group of jenkins instance




Log labels:
--info
--warning
--debug
--error

===================================================================
Credentials:

in jenkins master go to:
manage jenkins >> credentials >> system >> Global credential >> 

username: github username
password: github token
id: name from cred

=====================================================================

