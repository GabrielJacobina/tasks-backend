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
                deploy adapters: [tomcat8(credentialsId: '3daf4377-1981-4a39-89c5-a8495c0f6534', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
         }
         stage ('API Test') {
             steps {
                dir('api-test') {
                    git credentialsId: '427a78da-15e8-4f69-98a3-cbbf8b9885c1', url: 'https://github.com/GabrielJacobina/tasks-api-test'
                    sh 'mvn test'
                }
             }
         }
         stage ('Deploy Frontend') {
             steps {
                 dir('frontend') {
                     git credentialsId: '427a78da-15e8-4f69-98a3-cbbf8b9885c1', url: 'https://github.com/GabrielJacobina/tasks-frontend'
                     sh 'mvn clean package'
                     deploy adapters: [tomcat8(credentialsId: '3daf4377-1981-4a39-89c5-a8495c0f6534', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                 }
             }
         }
         stage ('Functional Test') {
             steps {
                 dir('functional-test') {
                     git credentialsId: '427a78da-15e8-4f69-98a3-cbbf8b9885c1', url: 'https://github.com/GabrielJacobina/tasks-functional-test'
                     sh 'mvn test'
                 }
             }
         }
    }


}
