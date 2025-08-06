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
                git branch: 'local-dev-uc', url: 'https://github.com/udaychopade27/3-Tier-DevSecOps-Mega-Project.git'
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
        
        
    }
}
