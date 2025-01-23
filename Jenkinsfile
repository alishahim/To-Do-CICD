pipeline {
    agent any
    environment {
        EC2_IP = '44.213.99.31'
        EC2_USER = 'ubuntu'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Inno-Tech-Academy/To-Do-CICD.git'
            }
        }
        stage('Build') {
            steps {
                cd todo
                sh 'npm install'
                sh 'npm run build'
            }
        }
        stage('Deploy') {
            steps {
                sshagent(['credential-id']) {
                    sh """
                    scp -r todo/build ${EC2_USER}@${EC2_IP}:/home/${EC2_USER}/react-app
                    ssh ${EC2_USER}@${EC2_IP} << EOF
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