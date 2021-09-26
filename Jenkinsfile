pipeline {
    agent any
    stages {
        stage ('Build Backend') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }
         stage ('Unit Tests') {
             steps {
                sh 'mvn test'
             }
         }
         stage ('Sonar Analysis') {
             environment {
               scannerHome = tool 'SONAR_SCANNER'
             }
              steps {
                 withSonarQubeEnv('SONAR_LOCAL'){
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://192.168.0.107:9000 -Dsonar.login=c8c1e1428a3f0c1c0e4198bc6bb59b52f5189348 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/src/test/**,**/model/**,**Application.java"
                 }
              }
         }
         stage ('Quality Gate') {
            steps {
                sleep(10)
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
         }
         stage ('Deploy Backend') {
            steps {
                deploy adapters: [tomcat8(credentialsId: '427a78da-15e8-4f69-98a3-cbbf8b9885c1', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
         }
    }


}
