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

## Docker as Agent
- How to Setup Docker Containers As Build Agents for Jenkins : https://www.youtube.com/watch?v=ymI02j-hqpU
- github repo : https://github.com/aymendr/jenkins-example-docker
- Install **Docker pipeline** plugin
- Install Docker Desktop on your machine: https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe?utm_source=docker&utm_medium=webreferral&utm_campaign=dd-smartbutton&utm_location=module&_gl=1*s1o9kg*_ga*Njk0NjA2ODA0LjE3MDUzNDAwMTk.*_ga_XJWPQMJYHQ*MTcwOTM3MDQ0OS4xNC4xLjE3MDkzNzA0NTIuNTcuMC4w
- 
## Deploy artifacts to destinations through Jenkins
- Install the "Publish Over SSH" plugin https://plugins.jenkins.io/publish-over-ssh/
- Configure the server connection : Manage jenkins --> System --> SSH Servers --> fill in Name, Hostname (ip), username (with wich you will connect to server), Advanced --> Tick the option : **Use password authentication, or use a different key** --> give username password
- In Job configuration, addd build step **Send files or execute commands over SSH** --> choose server name --> Source files wil contain regex expressions (current dir is the root dir for a maven project) exp: target/*.jar --> Remove prefix : target (to not create the folder target)

## Install jenkins as Docker container
- Install Docker on Centos9: https://docs.docker.com/engine/install/centos/
- Permission denied error --> Add user to docker group : ``` sudo usermod -aG docker aymen ```
- Start Jenkins as docker container : https://github.com/jenkinsci/docker/blob/master/README.md
