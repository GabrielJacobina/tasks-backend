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
                timeout(time: 1, unit: 'MINUTES'){
                    waitForQualityGate abortPipeline: true
                }
            }
         }
    }
}
