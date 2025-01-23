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
                    // Add the EC2 host key to known_hosts
                    sh """
                    ssh-keyscan -H ${EC2_IP} >> ~/.ssh/known_hosts
                    """
                    // Deploy the React app and restart Nginx
                    sh """
                    ssh ${EC2_USER}@${EC2_IP} << 'EOF'
                    sudo rm -rf /var/www/html/*
                    sudo cp -r /home/${EC2_USER}/react-app/* /var/www/html/
                    EOF
                    """
                }

            }
        }

    }
}