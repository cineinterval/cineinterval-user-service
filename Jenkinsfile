pipeline {
    agent any

    environment {
        REMOTE_USER = "ec2-user"
        REMOTE_HOST = "3.238.190.154"
        PEM_FILE = "/var/lib/jenkins/cineinterval-key.pem"
        APP_PASSWORD = "Welcome123"  // 🔐 Injected securely here
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

        stage('Show Injected Password (Simulated Runtime Use)') {
            steps {
                sh '''
                echo "Simulated runtime password is: $APP_PASSWORD"
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

