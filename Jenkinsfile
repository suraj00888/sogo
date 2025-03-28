pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws_access_key') // Replace with your Jenkins credential ID
        AWS_SECRET_ACCESS_KEY = credentials('aws_secret_key') // Replace with your Jenkins credential ID
        SES_SENDER = 'surajmali430@gmail.com'  // Must be a verified email in AWS SES
        SES_RECIPIENT = 'surajmali430@gmail.com'
        SMTP_SERVER = 'email-smtp.us-east-1.amazonaws.com'  // Change based on AWS region
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/suraj00888/sogo.git' // Replace with your repo URL
            }
        }

        stage('Terraform Init') {
            steps {
                script {
                    def startTime = System.currentTimeMillis()
                    bat 'terraform init'
                    def endTime = System.currentTimeMillis()
                    env.INIT_TIME = (endTime - startTime) / 1000 + " seconds"
                }
            }
        }

        stage('Terraform Validate') {
            steps {
                bat 'terraform validate'
            }
        }

        stage('Terraform Plan') {
            steps {
                script {
                    def startTime = System.currentTimeMillis()
                    bat 'terraform plan -out=tfplan'
                    def endTime = System.currentTimeMillis()
                    env.PLAN_TIME = (endTime - startTime) / 1000 + " seconds"
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                script {
                    input message: 'Do you want to apply the Terraform changes?'
                    def startTime = System.currentTimeMillis()
                    bat 'terraform apply -auto-approve tfplan'
                    def endTime = System.currentTimeMillis()
                    env.APPLY_TIME = (endTime - startTime) / 1000 + " seconds"
                }
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
            script {
                emailext(
                    subject: "Jenkins Pipeline Success - ${env.JOB_NAME}",
                    body: """Hello Team,<br><br>
                    The Jenkins pipeline has completed successfully.<br>
                    <b>Pipeline:</b> ${env.JOB_NAME}<br>
                    <b>Build Number:</b> ${env.BUILD_NUMBER}<br>
                    <b>Build URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a><br>
                    <b>Status:</b> <span style="color:green;"><b>SUCCESS ✅</b></span><br><br>
                    <b>Execution Times:</b><br>
                    <table border="1" cellspacing="0" cellpadding="5">
                        <tr><th>Stage</th><th>Time Taken</th></tr>
                        <tr><td>Terraform Init</td><td>${env.INIT_TIME}</td></tr>
                        <tr><td>Terraform Plan</td><td>${env.PLAN_TIME}</td></tr>
                        <tr><td>Terraform Apply</td><td>${env.APPLY_TIME}</td></tr>
                    </table><br><br>
                    Regards,<br>
                    Jenkins""",
                    to: "${env.SES_RECIPIENT}",
                    replyTo: "${env.SES_SENDER}",
                    from: "${env.SES_SENDER}",
                    mimeType: 'text/html'
                )
            }
        }
        failure {
            echo 'Terraform deployment failed!'
            script {
                emailext(
                    subject: "Jenkins Pipeline Failure - ${env.JOB_NAME}",
                    body: """Hello Team,<br><br>
                    The Jenkins pipeline has <b style="color:red;">FAILED ❌</b>.<br>
                    <b>Pipeline:</b> ${env.JOB_NAME}<br>
                    <b>Build Number:</b> ${env.BUILD_NUMBER}<br>
                    <b>Build URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a><br>
                    <b>Status:</b> <span style="color:red;"><b>FAILURE ❌</b></span><br><br>
                    Please check the logs for more details.<br><br>
                    Regards,<br>
                    Jenkins""",
                    to: "${env.SES_RECIPIENT}",
                    replyTo: "${env.SES_SENDER}",
                    from: "${env.SES_SENDER}",
                    mimeType: 'text/html'
                )
            }
        }
    }
}
