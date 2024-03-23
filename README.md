# fcit-Jenkins
## Install Jenkins on Centos 9
- get Jenkins official repository
``` curl https://pkg.jenkins.io/redhat-stable/jenkins.repo > /etc/yum.repos.d/jenkins.repo ```
- get GPG key and install Jenkins
``` rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key ```
- install jenkins
``` dnf -y install jenkins ```
- If Firewalld is running, allow service ports.
```
firewall-cmd --add-port=8080/tcp --permanent
firewall-cmd --reload
```
## Install Jenkins on ubuntu 22.04
Ref : https://www.cherryservers.com/blog/how-to-install-jenkins-on-ubuntu-22-04
### Install Java
- Let’s install OpenJDK 11, which is an open-source Java Platform. Use the following command to continue:
- Documentation : https://www.jenkins.io/doc/book/installing/linux/
```sudo apt install openjdk-11-jdk```
### Install Jenkins
- Long Term Support release :
```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```
### Start Jenkins
- You can start Jenkins by running the following systemctl command:
```sudo systemctl start Jenkins```
- Use the systemctl status command to verify that Jenkins started successfully.
```sudo systemctl status Jenkins```
### Setup Jenkins
- To set up Jenkins, go to your browser and open http://localhost:8080 where Jenkins server is running.
- The Unlock Jenkins screen will appear, asking for the administrator password.
- Open a new tab in the terminal and run the command below to get the initial credential to unlock Jenkins.
```sudo cat /var/lib/jenkins/secrets/initialAdminPassword```
### Configure Firewall
- Jenkins is configured to run on port 8080 by default, so you should open that port by using ufw.
```sudo ufw allow 8080```
- Run the commands below to allow ssh and enable ufw:
```
sudo ufw allow ssh
sudo ufw enable
```
## Jenkins CLI
link : https://www.youtube.com/watch?v=LwwWc7eoLk4
- go to Manage Jenkins --> Jenkins CLI
- Download Jenkins CLI jar
- Type the command : ``` java -jar jenkins-cli.jar -s http://localhost:8080/ help ``` to display the help
- To get info about user connected  : ``` java -jar jenkins-cli.jar -s http://localhost:8080/ -auth admin:admin who-am-i ```
- Build a job with username/pwd auth : ``` java -jar jenkins-cli.jar -s http://localhost:8080/ -auth admin:admin build hello-world ```
- To generate a token : profile --> Configure --> API Token --> Add new Token --> give a name --> Generate : 111d60c3242e1557859e1c6b9277889e23
- Build now the job hello-world with the jenkins-cli : ``` java -jar jenkins-cli.jar -s http://localhost:8080/ -auth admin:111d60c3242e1557859e1c6b9277889e23 build hello-world -s -v ```
- Install plugin : ``` java -jar jenkins-cli.jar -s http://localhost:8080/ -auth admin:111d60c3242e1557859e1c6b9277889e23 install-plugin terraform ```
- Safe restart : ``` java -jar jenkins-cli.jar -s http://localhost:8080/ -auth admin:111d60c3242e1557859e1c6b9277889e23 safe-restart ```

## Atlassian JIRA
- JIRA website : https://www.atlassian.com/
- create an account and log in
- Create JIRA Cloud Token : https://support.atlassian.com/atlassian-account/docs/manage-api-tokens-for-your-atlassian-account/
``` ATATT3xFfGF0vqcZvBURliFRkD2kPTKX7mq2myxnKq8N7gDhKtqle1MpCAGdWJQ4YBbHVwc-l3___vpBsoHDzVM4UxH19tjYC3fg63l6gRWvhzo8crgLwgYuK_GMMOVoNY81Nuh4Kp8QpqC7bUEsKmoL3FRnW4RCHK81yJvRZAAEMnPiEQ4_tVE=5796E447 ```
- Add a comment to an issue : https://www.youtube.com/watch?v=-KrlCWVPfJM / in pipeline syntax --> Sample step --> JIRA comment
- Tag an issue with job build link : https://www.youtube.com/watch?v=zFy9hM5uRjw /  Job configuration  --> post-build actions --> Issue selector : Explicit selector --> jira issue ID

## Deploy artifacts to destinations through Jenkins
- Install the "Publish Over SSH" plugin https://plugins.jenkins.io/publish-over-ssh/
- Configure the server connection : Manage jenkins --> System --> SSH Servers --> fill in Name, Hostname (ip), username (with wich you will connect to server), Advanced --> Tick the option : **Use password authentication, or use a different key** --> give username password
- In Job configuration, addd build step **Send files or execute commands over SSH** --> choose server name --> Source files wil contain regex expressions (current dir is the root dir for a maven project) exp: target/*.jar --> Remove prefix : target (to not create the folder target)

## Install jenkins as Docker container
- Install Docker on Centos9: https://docs.docker.com/engine/install/centos/
- Permission denied error --> Add user to docker group : ``` sudo usermod -aG docker aymen ```
- Start Jenkins as docker container : https://github.com/jenkinsci/docker/blob/master/README.md
- Start Jenkins with docker in detached mode with volume ``` docker run -d -v jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 --restart=on-failure jenkins/jenkins:lts-jdk17 ```
- Enter jenkins container ``` docker exec -it <container_id> bash ```
- display init password ``` cat /var/jenkins_home/secrets/initialAdminPassword ```

## Roles and users
- Go to manage Jenkins > Users > Add new users 
- Install **Role-based Authorization Strategy** plugin : https://plugins.jenkins.io/role-strategy/
![image](https://github.com/aymendr/fcit-Jenkins/assets/1395829/51bfb04c-85a9-44cc-b4e4-1995df89aa39)
- Go to manage Jenkins > Security > Authorization > Role-Based Strategy
![image](https://github.com/aymendr/fcit-Jenkins/assets/1395829/cf5222e7-1267-4273-9956-cbc76e6c64a4)
- Go to manage Jenkins > Manage and Assign Roles > Manage Roles
- Add a global role **dev** and tick **Overall > Read** permission and save
- Switch to Assign Roles > add users to new global group to give them read permissions
### filtering roles by jobs template
- Go to manage Jenkins > Manage and Assign Roles > Manage Roles > Add item roles with patterns
![image](https://github.com/aymendr/fcit-Jenkins/assets/1395829/79eef35d-eb76-41a4-8e8e-b5ca11836aea)
- Switch to Assign Roles > Item roles > add users to job roles with templates and save
![image](https://github.com/aymendr/fcit-Jenkins/assets/1395829/8ced744b-0515-4e86-869a-dcdf7d4ca6b3)

## Trigger & Remote url
- Configure Job > Triggers > Build after other projects are built > fill in with job name
![image](https://github.com/aymendr/fcit-Jenkins/assets/1395829/41516181-1164-489b-ab21-9c688adb084a)

- Build second job and check if first job is build
- Configure Job > Triggers > Triggers build remotely > fill in Authentication Token > save
![image](https://github.com/aymendr/fcit-Jenkins/assets/1395829/338fc54f-2b1c-4cc5-8f32-b62cfb814ba7)
- Access url : http://localhost:8080/job/job1/build?token=1234 > check if job1 is triggered

## Planifications & Crons
![image](https://github.com/aymendr/fcit-Jenkins/assets/1395829/5b2fea38-b9dd-4d34-9512-129270ac8a9b)
MINUTE HOUR DOM MONTH DOW
Exp : 
Every fifteen minutes (perhaps at :07, :22, :37, :52):
H/15 * * * *

## Paramètres et jobs
- Configure Job > General > This project is parameterized > password > fill in with parameter name and value
- To display, add a build step > execute a bat script on windows > ```echo %<var_name>%``` | for linux ```echo $<var_name>```
- add password parameter or boolean or a list by choosing **Choice Parameter** or **File Parameter** to upload a file for the build 
## Exercice d'application
- créer 3 jobs dépendant les uns des autres, un pour builder un hello world en java, le deuxième pour un run et redirection du résultat vers un fichier .txt et un troisième pour tester le résultat en utilisant la commande grep

## Repo Git et build
- url : https://github.com/aymendr/jenkins-helloworld.git
script :
```
git clone https://github.com/aymendr/jenkins-helloworld.git
cd jenkins-helloworld
javac Main.java
java Main
```

- En utilisant le plugin git :
```
javac Main.java
java Main 
```
## Git Push Tags
- Configure git repo with credentials (username/password with token in password)
- create shell script :
```
git config --global user.email "driraaymen@gmail.com"
git config --global user.name "aymen"
javac Main.java
java Main
```
- configure Post-build **Git Publisher** Action
![image](https://github.com/aymendr/fcit-Jenkins/assets/1395829/2869da49-60a3-4ee2-8e44-e3e7cdcbdbed)
- Configure branch to push to when build done
![image](https://github.com/aymendr/fcit-Jenkins/assets/1395829/756f4c42-02f4-4a64-8b3e-31b5ba491750)

## Pipeline Delivery Plugin
- Install the plugin **Pipeline Delivery**
- Create new View > Delivery Pipeline View
- Configure the view and add the components

## Pipeline Syntax
### Pipeline declaratif
- create new pipeline job and paste the following groovy code:
```
pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                sh 'rm -rf *'
                sh 'git clone https://github.com/aymendr/jenkins-helloworld.git'
            }
        }
        stage('Build') {
            steps {
                sh 'cd jenkins-helloworld && javac Main.java'
            }
        }
        stage('Test') {
            steps {
                sh 'cd jenkins-helloworld && java Main'
            }
        }
    }
}
```

### Pipeline scripté
- create new pipeline job and paste the following scripted code:
```
node {
    stage('Clone') {
        git branch: 'main', url: 'https://github.com/aymendr/jenkins-helloworld.git'
    }
    stage('Build') {
        sh 'javac Main.java'
    }
    stage('Test') {
        sh 'java Main'
    }
}
```

## Jenkinsfile
copy pipeline code in a Jenkinsfile file and commit this file on your github repo
- create a pipeline job and use **Pipeline Script from SCM**
![image](https://github.com/aymendr/fcit-Jenkins/assets/1395829/ebff6d0a-8e68-4b60-b7c6-0f03b41f536b)

## Docker Agent on Jenkins
### Install Docker
link : https://docs.docker.com/engine/install/ubuntu/
### Setup pipeline with docker agent
- Install **Docker Pipeline** plugin
- Add jenkins user to docker group to allow docker commands: ```sudo usermod -aG docker jenkins```
- Restart Jenkins
- create pipeline job and put the following declarative script:
```
pipeline {
    agent {
        docker { 
            image 'nginx:latest' 
            args '-p 80:80'
        }
    }
    stages {
        stage('Test') {
            steps {
                sh 'cat /etc/nginx/conf.d/default.conf'
            }
        }
    }
}
```
- create pipeline job and put the following scripted pipeline:
```
node {
    stage('Show running docker containers') {
        docker.image('nginx:latest').withRun('-p 80:80') {
            sh 'docker ps'
            sh 'curl localhost'
        }
    }
}
```
### Setup pipeline with docker agent with Jenkinsfile
repo url : https://github.com/aymendr/jenkins-build-docker/tree/master

## Push Docker Image to registry
- create a pipeline and paste the following script:
- create **gitlab_credentials** (username/password) to use it int the pipeline
```
node{
    stage('Clone') {
        git 'https://github.com/aymendr/jenkins-build-docker.git'
    }

    def img = stage('Build image') {
        docker.build("registry.gitlab.com/driraaymen/presentation_jenkins:nginx-$BUILD_ID")
    }
    
    stage('Test'){
        img.withRun("--name nginx-$BUILD_ID -p 80:80"){
            sh 'curl localhost'
        }
    }
    
    stage('Build - Push') {
        docker.withRegistry('https://registry.gitlab.com', 'gitlab_credentials') {
            img.push()
        }        
    }
}
```
## WarTest
- Repo to clone : https://github.com/aymendr/war-build-docker.git
pipeline code :
```
node{
    def registryProject='registry.gitlab.com/driraaymen/presentation_jenkins/wartest'
    def IMAGE="${registryProject}:version-$BUILD_ID"
    stage('Clone') {
        git 'https://github.com/aymendr/war-build-docker.git'
    }
    
    stage('Maven package'){
        withMaven(globalMavenSettingsConfig: '', jdk: '', maven: 'maven', mavenSettingsConfig: '', traceability: true) {
            sh 'mvn clean package'
        }
    }

    def img = stage('Build image') {
        docker.build("$IMAGE")
    }
    
    stage('Test'){
        img.withRun("--name run-$BUILD_ID -p 8081:8080"){
            sh 'docker ps'
            sh 'netstat -ntaup'
            sh 'sleep 30s'
            sh 'curl localhost:8081'
            sh 'docker ps'
        }
    }
    
    stage('Build - Push') {
        docker.withRegistry('https://registry.gitlab.com', 'gitlab_credentials') {
            img.push()
        }        
    }
}
```
- To build maven project configure the maven installation in the jenkins > manage > Tools
- Install **Pipeline Maven Integration Plugin**
- use Pipeline Syntax > withMaven to figureout how to call maven commands
# Index
- To delete all containers including its volumes:
```docker rm -vf $(docker ps -aq)```
- To delete all the images:
```docker rmi -f $(docker images -aq)```
