pipeline {

    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    } 
    environment {
        withCredentials([string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'access_key'), string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'secret_access_key')]) {
            AWS_ACCESS_KEY_ID     = '$access_key'
            AWS_SECRET_ACCESS_KEY = '$secret_access_key'
        }   
    }

    agent any
    stages {
        stage('checkout') {
            steps {
                script {
                    dir('terraform') {
                        git 'https://github.com/anhpvhe/TF-Jenkins-Devops-01.git'
                    }
                }
            }
        }

        stage('Plan') {
            steps {
                bat 'cd terraform && terraform init'
                bat 'cd terraform && terraform plan -out tfplan'
                bat 'cd terraform && terraform show -no-color tfplan > tfplan.txt'
            }
        }

        stage('Approval') {
            when {
                not {
                    equals expected: true, actual: params.autoApprove
                }
            }
            steps {
                script {
                    def plan = readFile 'terraform/tfplan.txt'
                    input message: "Do you want to apply the plan?",
                    parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
                }
            }
        }

        stage('Apply') {
            steps {
                bat 'cd terraform && terraform apply -input=false tfplan'
            }
        }
    }
}
