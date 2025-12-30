pipeline {
    agent any

    environment {
        TF_IN_AUTOMATION = "true"
        AWS_DEFAULT_REGION = "us-east-1"
    }

    options {
        disableConcurrentBuilds()
        timestamps()
    }

    stages {

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Verify Workspace') {
            steps {
                sh '''
                echo "Current directory:"
                pwd
                echo "Files in workspace:"
                ls -la
                echo "Modules:"
                ls -la modules
                '''
            }
        }

        stage('Terraform Init') {
            steps {
                sh '''
                terraform init -input=false
                '''
            }
        }

        stage('Terraform Validate') {
            steps {
                sh '''
                terraform validate
                '''
            }
        }

        stage('Terraform Plan') {
            steps {
                sh '''
                terraform plan -out=tfplan
                '''
            }
        }

        stage('Approval') {
            steps {
                input message: 'Approve Terraform Apply?', ok: 'Apply'
            }
        }

        stage('Terraform Apply') {
            steps {
                sh '''
                terraform apply -input=false tfplan
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Terraform pipeline completed successfully"
        }
        failure {
            echo "❌ Terraform pipeline failed"
        }
        always {
            sh 'terraform version || true'
        }
    }
}

