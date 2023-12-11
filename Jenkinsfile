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
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AAAziiz/spring-mongodb-pipeline']])
                sh 'mvn clean install'
                sh 'docker build -t azziiz/springboot .'
            }
        }

         stage('Build docker-compose image'){
            steps{
                script{
                    sh 'docker-compose -f docker-compose.yml build'
                    sh 'docker tag azziiz/springboot azziiz/springboot:latest'
                }
            }
        }
         stage('Push to docker hub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhub')]) {
                        
                    sh 'docker login -u azziiz -p ${dockerhub} docker.io'
    }                      
                    sh 'docker push azziiz/springboot:latest'
                    
                }
            }
        }
    }
        
    }

