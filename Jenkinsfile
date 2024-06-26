pipeline {

    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    }
    environment {
        GIT_URL = 'https://github.com/anhpvhe/TF-Jenkins-Devops-01.git'
        GIT_BRANCH = 'main'
        TERRAFORM_PATH = 'C:\\Terraform\\terraform.exe'
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    } 

    agent any
    stages {
        stage('checkout') {
            steps {
                script {
                    dir('terraform') {
                        git branch: GIT_BRANCH, url: GIT_URL, credentialsId: 'git-credentials-id'
                    }
                }
            }
        }

        stage('Plan') {
            steps {
                withCredentials([
                    string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    // bat 'cd Terraform && terraform init'
                    // bat 'cd Terraform && terraform plan -out tfplan'
                    // bat 'cd Terraform && terraform show -no-color tfplan > tfplan.txt'
                    // bat 'cd Terraform && C:\\path\\to\\terraform.exe init'
                    // bat 'cd Terraform && C:\\path\\to\\terraform.exe plan -out tfplan'
                    // bat 'cd Terraform && C:\\path\\to\\terraform.exe show -no-color tfplan > tfplan.txt'
                    // bat "${env.TERRAFORM_PATH} -version"
                    bat "cd terraform && ${env.TERRAFORM_PATH} init"
                    bat "cd terraform && ${env.TERRAFORM_PATH} plan -out tfplan"
                    bat "cd terraform && ${env.TERRAFORM_PATH} show -no-color tfplan > tfplan.txt"
                }
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
                withCredentials([
                    string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    bat "cd terraform && ${env.TERRAFORM_PATH} apply -input=false tfplan"
                }
            }
        }
        stage('Stop') {
            steps {
                input message: 'Do you want to destroy the infrastructure?', ok: 'Destroy'
            }
        }
    }

    post {
        always {
            script {
                bat "cd terraform && ${env.TERRAFORM_PATH} destroy -auto-approve"
            }
        }
    }
}