pipeline {
    agent any

    environment {
        REMOTE_USER = "ec2-user"
        REMOTE_HOST = "54.84.71.226"
        PEM_FILE = "/var/lib/jenkins/cineinterval-key.pem"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/cineinterval/cineinterval-user-service.git'
            }
        }

        stage('Security Scan') {
            steps {
                sh '''
                echo "Running simulated security scan..."
                if grep -i 'password' index.html; then
                    echo "❌ Hardcoded password found!"
                    exit 1
                else
                    echo "✅ No hardcoded passwords."
                fi
                '''
            }
        }

        stage('Fetch Secret from AWS Secrets Manager') {
            steps {
                script {
                    env.APP_PASSWORD = sh(script: "aws secretsmanager get-secret-value --secret-id cineinterval/user-service-password --query SecretString --output text", returnStdout: true).trim()
                }
                sh '''
                echo "🟢 Secret fetched from AWS: $APP_PASSWORD"
                '''
            }
        }

        stage('Deploy to user-ec2') {
            steps {
                sh """
                echo "Transferring index.html to user-ec2..."
                scp -i ${PEM_FILE} -o StrictHostKeyChecking=no index.html ${REMOTE_USER}@${REMOTE_HOST}:/home/ec2-user/
                """
            }
        }
    }
}

