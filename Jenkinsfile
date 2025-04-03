pipeline {
    agent any

    environment {
        REMOTE_USER = "ec2-user"
        REMOTE_HOST = "3.230.145.189"
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
                    echo "❌ Password found in code!"
                    exit 1
                else
                    echo "✅ No secrets found."
                fi
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
