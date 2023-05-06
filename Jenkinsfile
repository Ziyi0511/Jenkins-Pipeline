pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                // Compile and package code using Maven
                sh 'mvn clean package'
            }
        }
        stage('Unit and Integration Tests') {
            steps {
                // Run JUnit and Selenium tests
                sh 'mvn test'
            }
        }
        stage('Code Analysis') {
            steps {
                // Use SonarQube for code quality analysis
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage('Security Scan') {
            steps {
                // Use OWASP ZAP or Snyk for security scan
                sh 'zap-cli --zap-url http://localhost -f zap-report.html -t https://localhost:8080'
            }
        }
        stage('Deploy to Staging') {
            steps {
                // Deploy to an AWS EC2 instance
                sh 'aws ec2 deploy-to-staging'
            }
        }
        stage('Integration Tests on Staging') {
            steps {
                // Run Selenium tests on staging environment
                sh 'mvn test'
            }
        }
        stage('Deploy to Production') {
            steps {
                // Deploy to an AWS EC2 instance
                sh 'aws ec2 deploy-to-production'
            }
        }
    }
}
