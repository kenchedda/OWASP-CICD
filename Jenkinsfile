pipeline {
    agent {
        node{
            label "build"
        }
    }
    
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar'
    }

    stages {
        stage('Git Checkout ') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/kenchedda/OWASP-CICD.git'
            }
        }
     
        stage('Sonarqube Analysis') {
            steps {
                    withSonarQubeEnv('sonar') {
                        sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Java-WebApp \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=Java-WebApp '''
    
                }
            }
        }

            stage('OWASP Dependency Check') {
            steps {
                   dependencyCheck additionalArguments: '--scan ./   ', odcInstallation: 'DP'
                   dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
      
        
        stage('Docker Build & Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                            sh "docker build -t webapp ."
                            sh "docker tag webapp kenappiah/webapp:latest"
                            sh "docker push kenappiah/webapp:latest "
 
                   }
                }   
            }
        }
        stage('Docker Image scan') {
            steps {
                    sh "trivy image dheeman29/webapp:latest "
            }
        }
        
         stage('Deploy Container using Docker Image') {
            steps {
                sh 'docker run -d --name springboot -p 8087:8087 kenappiah/webapp:latest'
            }
        }

        }
        
    }
