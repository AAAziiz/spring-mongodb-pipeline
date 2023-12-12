pipeline {
    agent any
    tools{ 
        maven 'Maven 3' 
      
    }
    //environment {
        //SCANNER_HOME=tool 'sonar-scanner' 
   // }
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
                sh 'mvn clean install'
                sh 'docker  build -t azziiz/springboot .'
            }
        }
       //  stage("Sonarqube Analysis "){ 
                   // steps{
                     //   withSonarQubeEnv('sonar-server') {
                          //  sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=student \
                           // -Dsonar.java.binaries=. \
                          //  -Dsonar.projectKey=student '''
                      //  }
                   // }
               // }
               // stage("quality gate"){
                  //  steps {
                       // script {
                         // waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                       // }
                  // }
               // }
               // stage ('Build war file'){
           // steps{
              //  sh 'mvn clean install -DskipTests=true'
           // }
      //  }
       // stage("OWASP Dependency Check"){
            //steps{
             //   dependencyCheck additionalArguments: '--scan ./ --format XML ', odcInstallation: 'DP-Check'
              //  dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
           // }
       // }

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
                   withKubeConfig([credentialsId: 'minikube kubeconfig']) {
                    sh 'minikube start'
                    sh 'minikube status'
                    //sh 'kubectl config use-context minikube' // Switch to Minikube context if needed
                    sh 'kubectl apply -f ./k8s/mongo-deployement.yml'
                    sh 'kubectl apply -f ./k8s/spring-deployement.yml'
                }
            }
        }
        }
         
        
    }
   // post {
      //  success {
        //    echo 'Deployment to Kubernetes and post-deployment checks succeeded!'
        //}

       // failure {
       //     echo 'Deployment to Kubernetes or post-deployment checks failed!'
        //}
   // }
        
    }

