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
        script {
            emailext(
                subject: "✅ Jenkins Pipeline Success - ${env.JOB_NAME}",
                body: """<html>
                <body style="font-family: Arial, sans-serif;">
                    <div style="border: 2px solid green; padding: 15px; border-radius: 10px; background-color: #e6ffe6;">
                        <h2 style="color: green;">✅ SUCCESS - Terraform Deployment Completed</h2>
                        <p>Hello Team,</p>
                        <p>The Jenkins pipeline has completed successfully.</p>
                        <table border="1" cellpadding="5" cellspacing="0" style="border-collapse: collapse;">
                            <tr><th style="background-color:#d4edda;">Pipeline</th><td>${env.JOB_NAME}</td></tr>
                            <tr><th style="background-color:#d4edda;">Build Number</th><td>${env.BUILD_NUMBER}</td></tr>
                            <tr><th style="background-color:#d4edda;">Build URL</th><td><a href="${env.BUILD_URL}" style="color: blue;">Click Here</a></td></tr>
                            <tr><th style="background-color:#d4edda;">Status</th><td><b style="color:green;">SUCCESS ✅</b></td></tr>
                        </table>
                        <br>
                        <p>🎉 Congratulations! Everything went smoothly.</p>
                        <p>Regards,<br>Jenkins</p>
                    </div>
                </body>
                </html>""",
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
                subject: "❌ Jenkins Pipeline Failure - ${env.JOB_NAME}",
                body: """<html>
                <body style="font-family: Arial, sans-serif;">
                    <div style="border: 2px solid red; padding: 15px; border-radius: 10px; background-color: #ffe6e6;">
                        <h2 style="color: red;">❌ FAILURE - Terraform Deployment Failed</h2>
                        <p>Hello Team,</p>
                        <p>The Jenkins pipeline has <b style="color:red;">FAILED ❌</b>.</p>
                        <table border="1" cellpadding="5" cellspacing="0" style="border-collapse: collapse;">
                            <tr><th style="background-color:#f8d7da;">Pipeline</th><td>${env.JOB_NAME}</td></tr>
                            <tr><th style="background-color:#f8d7da;">Build Number</th><td>${env.BUILD_NUMBER}</td></tr>
                            <tr><th style="background-color:#f8d7da;">Build URL</th><td><a href="${env.BUILD_URL}" style="color: blue;">Click Here</a></td></tr>
                            <tr><th style="background-color:#f8d7da;">Status</th><td><b style="color:red;">FAILURE ❌</b></td></tr>
                        </table>
                        <br>
                        <p>⚠️ Please check the logs for more details.</p>
                        <p>Regards,<br>Jenkins</p>
                    </div>
                </body>
                </html>""",
                to: "${env.SES_RECIPIENT}",
                replyTo: "${env.SES_SENDER}",
                from: "${env.SES_SENDER}",
                mimeType: 'text/html'
            )
        }
    }
}
