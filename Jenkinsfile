pipeline {
    agent any
    
    tools {
        nodejs 'Node 16'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
         stage('Install Newman') {
                    steps {
                         sh 'npm install -g newman newman-reporter-html'
                    }
                }
        stage('Run API Tests') {
            steps {
                sh 'mkdir -p newman-results'
                sh 'npm test'
            }
            post {
                always {
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'newman-results',
                        reportFiles: 'report.html',
                        reportName: 'API Test Report'
                    ])
                }
            }
        }
        
        stage('Deploy if Tests Pass') {
            when {
                expression {
                    currentBuild.result == null || currentBuild.result == 'SUCCESS'
                }
            }
            steps {
                echo 'Deploying to staging environment...'
                // Add actual deployment steps here
            }
        }
    }
    
    post {
        success {
            echo 'API Tests passed! Pipeline completed successfully.'
            // Send notification of successful build        }
        failure {
            echo 'API Tests failed! Deployment was skipped.'
            // Send notification of failed build
        }
    }
}
