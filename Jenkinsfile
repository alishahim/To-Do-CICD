pipeline {
    agent any
    tools {
        nodejs 'NodeJS_20'
    }
    environment {
        EC2_IP = '44.213.99.31'
        EC2_USER = 'ubuntu'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/aidiko20/To-Do-CICD.git'
            }
        }
        stage('Build') {
            steps {
                sh '''
                cd todo
                npm install
                npm run build
                '''
            }
        }
        stage('Deploy') {
            steps {
               sshagent(['credential-id']) {
                sh """
                scp -o StrictHostKeyChecking=no -r todo/build ${EC2_USER}@${EC2_IP}:/home/${EC2_USER}/react-app
                ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_IP} << EOF
                sudo rm -rf /var/www/html/*
                sudo cp -r /home/${EC2_USER}/react-app/* /var/www/html/
                sudo systemctl restart nginx
                EOF
                """
                }

            }
        }

    }
}