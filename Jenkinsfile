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

         stage('Build docker-compose  image'){
            steps{
                script{
                    sh 'docker-compose build'
                }
            }
        }
         stage('Push to docker hub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker-hub-cred', variable: 'dockerhubpwd')]) {
                        
                    sh 'docker login -u azziiz -p ${dockerhubpwd} docker.io'
    }                      
                    sh 'docker-compose push'
                    
                }
            }
        }
    }
        
    }
