# fcit-devSecOps

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