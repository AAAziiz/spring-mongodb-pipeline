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
                    withCredentials([string(credentialsId: 'doxker-token', variable: 'password')]) {

                    sh 'docker login -u azziiz -p ${password} docker.io'
    }                      
                    sh 'docker push azziiz/devops-integration '
                    
                }
            }
        }
    }
        
    }

