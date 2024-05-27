pipeline {
    agent any
    stages {
        stage('Test Credentials') {
            steps {
                withCredentials([
                    string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: '%AWS_ACCESS_KEY_ID%'),
                    string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    echo "AWS_ACCESS_KEY_ID: ${AWS_ACCESS_KEY_ID}"
                    echo "AWS_SECRET_ACCESS_KEY: %AWS_SECRET_ACCESS_KEY%"
                }
            }
        }
    }
}