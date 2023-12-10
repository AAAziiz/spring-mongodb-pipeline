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

         stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t azziiz/application .'
                }
            }
        }
         stage('Push to docker hub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'devops-project-credentials')]) {
                        
                    sh 'docker login -u azziiz -p ${devops-project-credentials} docker.io'
    }                      
                    sh 'docker push azziiz/application '
                    
                }
            }
        }
    }
        
    }

