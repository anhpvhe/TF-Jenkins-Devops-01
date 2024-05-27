pipeline {

    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    }
    environment {
        GIT_URL = 'https://github.com/anhpvhe/TF-Jenkins-Devops-01.git'
        GIT_BRANCH = 'main'
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
                withCredentials([string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'access_key'), string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'secret_access_key')]) {
                    script {
                        env.AWS_ACCESS_KEY_ID = access_key
                        env.AWS_SECRET_ACCESS_KEY = secret_access_key
                    }
                    bat 'cd terraform && terraform init'
                    bat 'cd terraform && terraform plan -out tfplan'
                    bat 'cd terraform && terraform show -no-color tfplan > tfplan.txt'
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
                withCredentials([string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'access_key'), string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'secret_access_key')]) {
                    script {
                        env.AWS_ACCESS_KEY_ID = access_key
                        env.AWS_SECRET_ACCESS_KEY = secret_access_key
                    }
                    bat 'cd terraform && terraform apply -input=false tfplan'
                }
            }
        }
    }
}
