    pipeline {
        agent {label 'chrome'}
        tools { 
          nodejs "NodeJS"
          dockerTool 'docker'
        }

        environment {
            App = ''
            registry = 'docker-services-training/aleeta/'
        }
        stages {
            stage('Clone Repository') {
                steps {
                    script {
                        checkout scm
                        sh 'node --version' 
                        sh 'docker --version'
                        sh 'npm --version'
                        echo "versions done"
                    }
                }
            }   
            stage('Build Image') {
                  steps {
                    script{
                       // App= docker.build("aleetadavis/nodejs-app")
                         docker.withTool('docker') {
                            docker.withRegistry('https://artifactory.dagility.com', 'aleeta-artifactory'){
                               App= docker.build(registry + "nodejs-app:latest")
                        echo "Build done"
                    }
                }
            }
                  }
            }
                            
                
            stage('Test image') {
                steps {
                    script{
                        sh 'npm install'
                        sh 'npm run test'
                        echo "Test completed"
		                
                    }
                }
            }
            stage('Push Image'){
                steps {
                    script{
                        docker.withTool('docker') {
                            docker.withRegistry('https://artifactory.dagility.com', 'aleeta-artifactory'){
                                App.push()
                                }
                        }
                        echo "Pushed image successfully to Artifactory"
                    }
                }
            } 
            stage(Deploy){
             steps{
                script{
                    docker.withTool('docker') {
                            docker.withRegistry('https://artifactory.dagility.com', 'aleeta-artifactory'){
                               sh 'docker container rm -f node-app-2 '
                               sh 'docker run --name node-app-2  --publish 8080:3000 docker-services-training/aleeta/nodejs-app:latest sleep 100 '
                               echo "Container created successfully"
                               sh 'docker image ls'
                               sh 'docker ps -a'
                               sh 'docker logs --details node-app-2'
                               //sh 'curl -I http://localhost:8000'
                            }
                        }
                    }
                }
            }        
        }        
    }
    
