pipeline {
    agent {  label "master"    }
    
    stages {
        // Step 1
        stage('SCM') {
                steps {
                    //checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[url: 'https://github.com/sabi-cryp/devops-mautic']]])
                      sh 'rm -rf project1-devops'

                // Clone the repository into a new directory
                      sh 'git clone https://github.com/sabi-cryp/project1-devops.git '
                    
            }      
        }
        // Step 2
        stage('Build by Maven') {
                steps {
                    dir('project1-devops') {
                        sh 'mvn clean package'
                    }
                }
        }
        
        // Step 3
        stage('Build docker image') {
                script {
                    dir('project1-devops') {
                        docker.build("app-java:latest")
                    }
                }
        }
        
        // Step 4
        stage('Push docker image') {
                steps {
                   
                    sh "sudo docker tag app-java:latest sabrineker/project1-devops/app-java:latest "
                   
                    sh "sudo docker push sabrineker/project1-devops/app-java:latest"
                }
        }
        
        // Step 5 
        stage('Deploy Java App in  Dev Env') {
                steps {
                        sh "sudo docker rm -f app-java"
                        sh "sudo docker run  -d -p 8084:8080 --name app-java sabrineker/project1-devops/app-java:latest"
                }
        }
        
            
        stage('QAT Test') {
            steps {
        	// bcz tomcat take some sec. to display data , so apply some delay here        
                retry(30) {
                    sh 'curl --silent http://10.10.204.7:8084/app-java/ |  grep tunisia'
                }   
            }
        }
        
    }
}      
