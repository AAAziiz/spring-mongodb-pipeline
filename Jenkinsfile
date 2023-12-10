pipeline {
    agent any
    tools{
        maven 'Maven 3'
      
    }
    stages{
         stage('Check Java Version') {
            steps {
                sh 'java -version'
            }
        }
        stage('Build Maven'){
            steps{
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AAAziiz/mongo-spring-pipeline']])
                sh 'mvn clean install'
            }
        }
        
    }
}
