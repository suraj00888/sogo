pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws_access_key') // Replace with your Jenkins credential ID
        AWS_SECRET_ACCESS_KEY = credentials('aws_secret_key') // Replace with your Jenkins credential ID
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/suraj00888/sogo.git' // Replace with your repo URL
            }
        }

        stage('Terraform Init') {
            steps {
                bat 'terraform init'
            }
        }

        stage('Terraform Validate') {
            steps {
                bat 'terraform validate'
            }
        }

        stage('Terraform Plan') {
            steps {
                bat 'terraform plan -out=tfplan'
            }
        }

        stage('Terraform Apply') {
            steps {
                input message: 'Do you want to apply the Terraform changes?'
                bat 'terraform apply -auto-approve tfplan'
            }
        }

        stage('Terraform Destroy') {
            steps {
                input message: 'Do you want to destroy the Terraform infrastructure?'
                bat 'terraform destroy -target=module.ec2.aws_instance.server -auto-approve'
                bat 'terraform destroy -auto-approve'
            }
        }
    }

    post {
        success {
            echo 'Terraform deployment executed successfully!'
            // Email notification for successful build
            emailext subject: "Jenkins Pipeline Success - ${env.JOB_NAME}",
                     body: """Hello Team,\n\n
                     The Jenkins pipeline has completed successfully.\n
                     Pipeline: ${env.JOB_NAME}\n
                     Build Number: ${env.BUILD_NUMBER}\n
                     Build URL: ${env.BUILD_URL}\n
                     Status: SUCCESS ✅\n\n
                     Regards,\n
                     Jenkins""",
                     to: 'surajmali430@gmail.com',
                     replyTo: 'surajmali430@gmail.com',
                     from: 'surajmali430@gmail.com',
                     mimeType: 'text/plain'
        }
        failure {
            echo 'Terraform deployment failed!'
            // Email notification for failed build
            emailext subject: "Jenkins Pipeline Failure - ${env.JOB_NAME}",
                     body: """Hello Team,\n\n
                     The Jenkins pipeline has **FAILED** ❌.\n
                     Pipeline: ${env.JOB_NAME}\n
                     Build Number: ${env.BUILD_NUMBER}\n
                     Build URL: ${env.BUILD_URL}\n
                     Status: FAILURE ❌\n\n
                     Please check the logs for more details.\n\n
                     Regards,\n
                     Jenkins""",
                     to: 'surajmali430@gmail.com',
                     replyTo: 'surajmali430@gmail.com',
                     from: 'surajmali430@gmail.com',
                     mimeType: 'text/plain'
        }
    }
}

   
