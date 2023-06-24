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

    Java:
       echo $JAVA_HOME
   Git:
       which git
   Maven:
       echo $M2_HOME
   docker
       install automatically
   
5) manage plugins
     git
     docker
     maven
     ecr
     Nexus Artifact Uploader
     SonarQube ScannerVersion
7) Jenkins CLI:
   Home dir: /var/lib/jenkins
   log dir: /var/log/jenkins/jenkins.log
   how to restart jenkins: service jenkins restart
8) Jenkins Job:
   freestyle
   maven project
   pipeline
   folder
   multibranch
9) Trigger in jenkins job:
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

Git:
type: usernamewithpassword
username: github username (AnujKumbhar911)
password: github token (go to general settings:>> developer settings>> personal access token

id: name from cred (	github_cred)

Dockerhub:
type: Secret text
id: dockerhub_cred
Secret text: dockerhub password

ecr:
type: AWS credentials:
id: aws-ecr-cred
create a user in aws for ecr-user >> go to security credentials >> create a access key >> third party access >> you get accesskey and secret access key

nexus:
add credentials for nexus in jenkins
 type: username with password
 username:admin
 pwd:admin
 id: nexus-credentials

Sonarqube:
type: secret text
Add the global credentials with the token which is earlier generated while creating the user jenkins in SonarQube.

=====================================================================

master Slave configuration:
1) Create a new jenkins slave ec2
2) install javaand maven
3) ssh jenkins slave # Create user and add the user to wheel group
    useradd jenkins-slave-01
4)# Create SSH Keys
sudo su - jenkins-slave-01
ssh-keygen -t rsa -N "" -f /home/jenkins-slave-01/.ssh/id_rsa
# The private and public keys will be created at these locations `/home/jenkins-slave-01/.ssh/id_rsa` and `/home/jenkins-slave-01/.ssh/id_rsa.pub`
5) cd .ssh
  cat id_rsa.pub > authorized_keys
  chmod 700 authorized_keys
6) Login to master and scan the key for jenkinsS
  mkdir -p /var/lib/jenkins/.ssh
  cd /var/lib/jenkins/.ssh
  ssh-keyscan -H (private ip of slave) >> /var/lib/jenkins/.ssh/known_hosts
  example: ssh-keyscan -H 172.31.13.85 >> /var/lib/jenkins/.ssh/known_hosts
  we get following:
    # 172.31.13.85:22 SSH-2.0-OpenSSH_7.4
    # 172.31.13.85:22 SSH-2.0-OpenSSH_7.4
    # 172.31.13.85:22 SSH-2.0-OpenSSH_7.4

  chown jenkins:jenkins known_hosts
  chmod 700 known_hosts

7) Step 6: Configure the Slave using Manage Jenkins on Master
     number of executers: 10
     Remote root directory: /home/jenkins-slave-01
     launch method: launch ssh agent via ssh
         host: (pvt ip of slave) 172.31.2.98
         cred: add a new cred :
             go to slave and follow:
             cd /home/jenkins-slave-02/.ssh
             cat id_rsa and copy entire key and add it to private key in private key with ssh and username jenkins-slave-01
             -----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEA0/Mgf2FkEExuufMeZ8APwbOsBqo9NNnLq5EMCIcqd8/X94ir
wc7VdNfNcHCbn6kcRVRCCJ2hzWAYnWX+d0xBxxvrZd6uCGC04Qp4ALC8DusllpvV
XtftUBOEFwnspyr540hlSlap7I0jXe1H6V2Jc/sBoWkat3/40HE5PKofO9zA7FBP
rc/gVc3fR3A3bV3a99Ol1ZASTjoHKTlaHEN4XjU4/4B9ZaTZyprdmgeFC3tIC6dd
9cSjaEo7+KMIQw9njqkv8qpaPjupIZDf+36/ZQIn9z0YVsL5VAAltXrGTZStlkm9
DZnFKv2SjUptJuZqUGLUHq8fO8uuNimgHdRg8QIDAQABAoIBAQCyGvyUDiMoDxJ8
iVpf9mdfKrokVAb2mIcjQAECgYEAmjozZcUch6IABzaLIwkU68mLK/iBDEu/nA70
0xrn6yzynbL2uGIdJrFh4R1ZrbmhM+4z6N46rcraUfusEPtMOwovmRXlQd7p/JZr
6ZPLQWBJAIvC8EzVsArNsKNn4tFWwHeuKpSeCnkrdqqzqj5RjatnxSzYJGNbdYFN
QmpRf2ECgYBn8UZDw0zZt59yk62Sp6mXCHk26h0XOcaVVeWR3yvwqLA9kTJBWo4S
JCsrL7rHO/JiApsUjgNmijV8OS6ehUNvh8r3AMq9i/cKZUP12p+YjFeglG4+AdFd
+WdbYkRJyo75g5QXXn79j5I/LES2WDT2LDuBTFbPIO6045oH+sf0EA==
-----END RSA PRIVATE KEY-----


new node is configured
===============================================================================
   




