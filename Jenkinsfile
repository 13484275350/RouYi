pipeline {
   agent any

   stages {
      stage('Check Out') {
         steps {
            echo 'Clone code start'
            git 'https://github.com/13484275350/RouYi'
            echo 'Clone code end'
         }
      }
      
      stage('Static Check') {
         steps {
            echo 'Static check start'
            sh "docker run -i --rm -v /home/jenkins/workspace/$JOB_NAME:/usr/src/mymaven -v $HOME/.m2:/root/.m2 -w /usr/src/mymaven maven mvn sonar:sonar \
                  -Dsonar.projectKey=RuoYi \
                  -Dsonar.host.url=http://139.217.229.117 \
                  -Dsonar.login=d320f551efe37e0ce2c8bc1d9489f44c42ceb695"
             echo 'Static check end'
         }
      }
      
      stage('Unit Test') {
         steps {
            echo 'Unit test start'
            sh "docker run -i --rm -v /home/jenkins/workspace/$JOB_NAME:/usr/src/mymaven -v $HOME/.m2:/root/.m2 -w /usr/src/mymaven maven mvn clean test"
            echo 'Unit test end'
         }
         post {
             always {
                 junit 'target/surefire-reports/*.xml'  //测试报告
             }
         }
      }
      stage('Package') {
         steps {
            echo 'Package start'
            // sh "sed -i -e 's/192.168.0.104/40.73.246.155/g' /var/jenkins_home/workspace/$JOB_NAME/src/main/resources/application-druid.yml"
            sh "docker run -i --rm -v /home/jenkins/workspace/$JOB_NAME:/usr/src/mymaven -v $HOME/.m2:/root/.m2 -w /usr/src/mymaven maven mvn clean package -Dmaven.test.skip=true"
            echo 'Package end'
         }
      }
      stage('Deploy to DEV') {
         steps {
            script{
                try{
                    sh 'docker rm -f java'
                }
                catch(err){
                         echo 'anyway'
                }
            }
            echo 'Deploy to DEV start'
            sh "docker run -i -p 8080:80 --name java -v /home/jenkins/workspace/Demo:/usr/src/myjava -w /usr/src/myjava -d java java -jar /usr/src/myjava/target/RuoYi.jar"
            sleep(40)
            echo 'Deploy to DEV end'
         }
      }
      stage('Test on DEV') {
         steps {
            echo 'Test on DEV start'
            sh "curl -L 40.73.246.155:8080"
			echo 'seleniumtest'
			sh "docker run --name python --rm -v /home/jenkins/workspace/Demo/target/seleniumtest:/usr/src/myselenium -w /usr/src/myselenium python:latest python /usr/src/myselenium/test.py"
			echo 'chrome.png'
            echo 'Test on DEV end'
         }
      }

      }
      post{
        success {
            emailext (
                subject: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: """<p>SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                    <p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>""",
                to: "13484275350@163.com",
                from: "1197440664@qq.com"
            )
         }
         failure {
            emailext (
                subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: """<p>FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                    <p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>""",
                to: "13484275350@163.com",
                from: "1197440664@qq.com"
            )
         }
      }
}