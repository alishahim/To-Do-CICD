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
                    sh '''
                    # Ensure target directories exist on EC2
                    ssh ${EC2_USER}@${EC2_IP} "mkdir -p /home/ubuntu/react-app"

                    # Copy the deploy.sh file to EC2
                    scp deploy.sh ${EC2_USER}@${EC2_IP}:/home/${EC2_USER}/deploy.sh

                    # Copy the build folder to EC2
                    scp -r todo/build ${EC2_USER}@${EC2_IP}:/home/${EC2_USER}/react-app

                    # Run the deploy.sh script on EC2
                    ssh ${EC2_USER}@${EC2_IP} "bash /home/${EC2_USER}/deploy.sh"
                    '''
                }

            }
        }

    }
}