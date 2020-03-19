# Continous Integration and Continous Deployment

![alt text](../imgs/Jenkins.jpg "dev team")

<br>

---

## Setup your Automation Server with AWS and Jenkins

Start up an EC2 instance, ssh into it and switch to the root user.

Install Java

``` bash
sudo yum -y install java-1.8*
```

Install wget
``` bash
sudo yum install wget
```

Install Jenkins
``` bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo # downloads the repo

sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key # imports the repo's public key

sudo yum install jenkins # installs jenkins
```

Start the Jenkins service
``` bash
sudo systemctl start jenkins.service
sudo systemctl status jenkins.service
```

Get the inital Jenkins password
``` bash
cat /var/lib/jenkins/secrets/initialAdminPassword # should return a long string
```

Update AWS and login to Jenkins
- Add 8080 as an inbound rule for the EC2 instance
- Go to the public IP:8080, enter the password, install the plugins and set credentials

<br>

---
## Build your pipeline

Install Chrome 
``` bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm

sudo yum install ./google-chrome-stable_current_*.rpm
```

Install Git
``` bash
dnf install git
```

Add NodeJS to Jenkins
- click manage jenkins 
- click manage plugins
- click the available tab
- choose nodejs
- choose download without restart

Configure NodeJS in Jenkins: 
- click manage jenkins 
- click Global Tool Configuration 
- add nodejs 10.16.1
- save

Establish a Pipeline
- Click New Item
- Name and choose "Pipeline"
- Discard old builds
- Add the Github project URL
- Build Triggers: Github hook trigger
- Copy and paste the sample pipeline

``` groovy
pipeline {
   agent any
   
   tools {nodejs "node"}

   stages {
      stage('clone the repo') {
         steps {
            git 'https://github.com/chazhall54/tr_qa_class'
         }
      }
      
      stage('Install dependencies') {
         steps {
            sh 'npm install'
         }
      }
      
     stage('run test') {
         steps {
            sh 'headless=true npm run test'
         }
      }
   }
}


```

<br>

---

## Reporting