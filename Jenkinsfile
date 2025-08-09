
pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/udaychopade27/3-Tier-DevSecOps-Mega-Project.git'
            }
        }
        
        stage('Frontend Compilation') {
            steps {
                dir('client') {
                    sh 'find . -name "*.js" -exec node --check {} +'
                }
            }
        }
        
        stage('Backend Compilation') {
            steps {
                dir('api') {
                    sh 'find . -name "*.js" -exec node --check {} +'
                }
            }
        }
        
        stage('GitLeaks Scan') {
            steps {
                sh 'gitleaks detect --source ./client'
                sh 'gitleaks detect --source ./api'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('Sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=NodeJS-Project \
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
        
        stage('Manual Approval for Production') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    input message: 'Approve deployment to PRODUCTION?', ok: 'Deploy'
                }
            }
        }
        
       stage('Deployment To Prod') {
            steps {
                script {
                    withKubeConfig(caCertificate: '', clusterName: 'devopsshack-cluster', contextName: '', credentialsId: 'k8-token-prod', namespace: 'prod', restrictKubeConfigAccess: false, serverUrl: 'https://D5A69030E35C79637C662E981E3D451E.gr7.ap-south-1.eks.amazonaws.com') {
                        sh 'kubectl apply -f k8s-prod/sc.yaml'
                        sleep 20
                        sh 'kubectl apply -f k8s-prod/database.yaml -n prod'
                        sh 'kubectl apply -f k8s-prod/backend.yaml -n prod'
                        sh 'kubectl apply -f k8s-prod/frontend.yaml -n prod'
                        sh 'kubectl apply -f k8s-prod/ci.yaml'
                        sh 'kubectl apply -f k8s-prod/ingress.yaml -n prod'
                        sleep 30
                    }
                }
            }
        }
        
        stage('Verify Deployment To Prod') {
            steps {
                script {
                    withKubeConfig(caCertificate: '', clusterName: 'devopsshack-cluster', contextName: '', credentialsId: 'k8-token-prod', namespace: 'prod', restrictKubeConfigAccess: false, serverUrl: 'https://D5A69030E35C79637C662E981E3D451E.gr7.ap-south-1.eks.amazonaws.com') {
                        sh 'kubectl get pods -n prod'
                        sleep 20
                         sh 'kubectl get ingress -n prod'
                        
                    }
                }
            }
        }
            
    }
}