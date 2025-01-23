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
                    # Add the EC2 host key to known_hosts
                    ssh-keyscan -H ${EC2_IP} >> ~/.ssh/known_hosts
                    # Install Nginx if not already installed
                    ssh ${EC2_USER}@${EC2_IP} << 'EOF'
                    sudo apt update
                    sudo apt install -y nginx
                    sudo systemctl start nginx
                    sudo systemctl enable nginx
                    EOF

                    # Create the web root directory if it doesn't exist
                    ssh ${EC2_USER}@${EC2_IP} << 'EOF'
                    sudo mkdir -p /var/www/html
                    sudo chmod 755 /var/www/html
                    EOF

                    # Copy React app to the web root directory
                    scp -r build ${EC2_USER}@${EC2_IP}:/home/${EC2_USER}/react-app
                    ssh ${EC2_USER}@${EC2_IP} << 'EOF'
                    sudo rm -rf /var/www/html/*
                    sudo cp -r /home/${EC2_USER}/react-app/* /var/www/html/

                    # Restart Nginx to reflect changes
                    sudo systemctl restart nginx
                    EOF
                    """
                }

            }
        }

    }
}