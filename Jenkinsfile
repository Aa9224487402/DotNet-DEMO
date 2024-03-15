pipeline {
    agent any
    
    tools{
        
        jdk 'jdk17'
    }
    
    environment {
        
        SCANNER_HOME= tool 'sonar'
    }

    stages {
        stage('Git Checkout ') {
            steps {
                git 'https://github.com/Aa9224487402/DotNet-DEMO.git'
            }
        }
        
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: ' --scan ./ ', odcInstallation: 'DC'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Trivy FS SCan') {
            steps {
                sh "trivy fs ."
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                
                withSonarQubeEnv('sonar'){
                  sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=dotnet-demo \
                    -Dsonar.projectKey=dotnet-demo ''' 
               }
                
               
            }
        }
        
        stage('Docker Build & Tag') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker') {
                        sh "make image"
                    }
                }
            }
        }
        
        stage('Docker Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker') {
                        sh "make push"
                    }
                }
            }
        }
        
        stage('Docker Deploy') {
            steps {
                sh "docker run -d -p 5000:5000 aryashersingh2/dotnet-demoapp"
            }
        }
        
        
    }
}
