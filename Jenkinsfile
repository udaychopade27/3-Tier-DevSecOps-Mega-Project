
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
         stage('K8-deploy') {
            steps {
                script {
                    withKubeConfig(caCertificate: '', clusterName: 'devopsshack-cluster', contextName: '', credentialsId: 'k8-token', namespace: 'dev', restrictKubeConfigAccess: false, serverUrl: 'https://58401D63AF7B16926BE2C779FE8FC69B.gr7.ap-south-1.eks.amazonaws.com') {
                            sh 'kubectl apply -f k8s-dev/sc.yaml -n dev'
                            sh 'kubectl apply -f k8s-dev/mysql.yaml -n dev'
                            sh 'kubectl apply -f k8s-dev/backend.yaml -n dev'
                            sh 'kubectl apply -f k8s-dev/frontend.yaml -n dev'
                            sleep 30
                        }
                }
            }
        }
        
        stage('verify-K8-deploy') {
            steps {
                script {
                    withKubeConfig(caCertificate: '', clusterName: 'devopsshack-cluster', contextName: '', credentialsId: 'k8-token', namespace: 'dev', restrictKubeConfigAccess: false, serverUrl: 'https://58401D63AF7B16926BE2C779FE8FC69B.gr7.ap-south-1.eks.amazonaws.com') {
                            sh 'kubectl get pods -n dev'
                            sh 'kubectl get svc -n dev'
                            
                        }
                }
            }
        }
    }
}