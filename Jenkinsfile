
pipeline {
    agent any
    tools {
        nodejs 'NodeJS'
    }
    environment {
        SONAR_SCANNER = tool 'sonar-scanner'
    }
    stages {
        stage('Git checkout') {
            steps {
                git branch: 'docker-build-uc', url: 'https://github.com/udaychopade27/3-Tier-DevSecOps-Mega-Project.git'
            }
        }
        stage('Frontend Compilitation') {
            steps {
                dir('client') {
                    sh 'find . -name "*.js" -exec node --check {} +'
                 }
            }
        }
        stage('backend Compilitation') {
            steps {
                dir('api') {
                    sh 'find . -name "*.js" -exec node --check {} +'
                 }
            }
        }
        stage('Gitleaks Scan') {
            steps {
                sh 'gitleaks detect --source ./client --exit-code 1'
                sh 'gitleaks detect --source ./api --exit-code 1'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('Sonar') {
                    sh ''' $SONAR_SCANNER/bin/sonar-scanner -Dsonar.projectName=NodeJS-Project \
                            -Dsonar.projectKey=NodeJS-Project '''
                }
            }
        }
        stage('Quality Gate Check') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs --format table -o fs-report.html .'
            }
        }
        stage('Build-Tag & Push Backend Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        dir('api') {
                            sh 'docker build -t uday27/backend:latest .'
                            sh 'trivy image --format table -o backend-image-report.html uday27/backend:latest '
                            sh 'docker push uday27/backend:latest'

                        }
                    }
                }
            }
        }  
            
        stage('Build-Tag & Push Frontend Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        dir('client') {
                            sh 'docker build -t uday27/frontend:latest .'
                            sh 'trivy image --format table -o frontend-image-report.html uday27/frontend:latest '
                            sh 'docker push uday27/frontend:latest'
                        }
                    }
                }
            }
             
        }  
        stage('Docker Deploy via Compose') {
            steps {
                script {
                    sh 'docker-compose -f docker-compose.yml up -d'
                }
            }
        } 
    }
}