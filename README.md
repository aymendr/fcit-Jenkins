# fcit-Jenkins

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

