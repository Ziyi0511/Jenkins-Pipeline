pipeline {
    agent any
    stages {
        stage('Build') {
            tools {
                maven 'Maven 3.6.3'
            }
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Unit and Integration Tests') {
            tools {
                maven 'Maven 3.6.3'
            }
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
                withCredentials([usernamePassword(credentialsId: 'Nessus Credentials', usernameVariable: 'NESSUS_USER', passwordVariable: 'NESSUS_PASSWORD')]) {
                    sh 'nessus_scan.sh --target=${env.BUILD_URL}'
                }
            }
        }
        stage('Deploy to Staging') {
            environment {
                STAGING_SERVER = 'ec2-user@staging-server'
            }
            steps {
                sshagent(['Staging Server Key']) {
                    sh 'ssh $STAGING_SERVER "cd /opt/myapp && git pull && mvn clean package && sudo systemctl restart myapp"'
                }
            }
        }
        stage('Integration Tests on Staging') {
            steps {
                sh 'run_integration_tests.sh http://staging-server:8080/myapp'
            }
        }
        stage('Deploy to Production') {
            environment {
                PRODUCTION_SERVER = 'ec2-user@production-server'
            }
            steps {
                sshagent(['Production Server Key']) {
                    sh 'ssh $PRODUCTION_SERVER "cd /opt/myapp && git pull && mvn clean package && sudo systemctl restart myapp"'
                }
            }
        }
    }
    post {
        always {
            emailext attachLog: true, body: "Pipeline status: ${currentBuild.result}", subject: "Pipeline Status", to: "youremail@example.com"
        }
    }
}

