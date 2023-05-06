pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Unit and Integration Tests') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Code Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage('Security Scan') {
            steps {
                withCredentials([string(credentialsId: 'ZAP_API_KEY', variable: 'ZAP_API_KEY')]) {
                    sh 'zap-cli --api-key=${ZAP_API_KEY} -t https://myapp.com -r report.html -x report.xml -J report.json'
                }
            }
        }
        stage('Deploy to Staging') {
            steps {
                withAWS(credentials: 'aws-creds', region: 'us-west-2') {
                    sh 'ansible-playbook deploy.yml'
                }
            }
        }
        stage('Integration Tests on Staging') {
            steps {
                sh 'mvn -Dtest.env=staging test'
            }
        }
        stage('Deploy to Production') {
            steps {
                withAWS(credentials: 'aws-creds', region: 'us-west-2') {
                    sh 'ansible-playbook deploy.yml'
                }
            }
        }
    }
}
