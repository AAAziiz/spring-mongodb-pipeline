pipeline {
    agent any
    tools{ 
        maven 'Maven 3' 
      
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner' 
    }
    stages{ 
         stage('Check Java Version') { 
            steps {
                sh 'java -version'
                echo 'java version is'
            }
        }
        stage('Build Maven'){ 
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AAAziiz/spring-mongodb-pipeline']])
                sh 'mvn clean package -DskipTests'
                sh 'docker  build -t azziiz/springboot .'
            }
        }
         stage("Sonarqube Analysis "){ 
                    steps{
                        withSonarQubeEnv('sonar-server') {
                            sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=student \
                            -Dsonar.java.binaries=. \
                            -Dsonar.projectKey=student '''
                        }
                    }
                }
                stage("quality gate"){
                    steps {
                        script {
                          waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                        }
                   }
                }
                stage ('Build war file'){
            steps{
                sh 'mvn clean install -DskipTests=true'
            }
        }
        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ --format XML ', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

         stage('Build docker-compose image'){
            steps{
                script{
                    sh 'docker  build -t azziiz/springboot .'
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
         stage('Pull to deployement'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhub')]) {
                        
                    sh 'docker login -u azziiz -p ${dockerhub} docker.io'
    }                      
                    sh 'docker pull azziiz/springboot:latest'
                    
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                   withKubeConfig([credentialsId: 'kubeconfig']) {
                    //sh 'minikube start'
                    //sh 'kubectl config use-context minikube' // Switch to Minikube context if needed
                    sh ' minikube kubectl -- apply -f ./k8s/mongo-deployment.yml '
                    sh ' minikube kubectl -- apply -f ./k8s/springboot-mongodb.yml '
                }
            }
        }
        }
         
        
    }
    post {
        success {
            echo 'Deployment to Kubernetes and post-deployment checks succeeded!'
        }

        failure {
            echo 'Deployment to Kubernetes or post-deployment checks failed!'
        }
    }
        
    }

