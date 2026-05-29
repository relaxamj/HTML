pipeline {
    agent any

    environment {
        EC2_HOST = "YOUR-EC2-IP"
        EC2_USER = "ec2-user"

        SOURCE_DIR = "./html/"
        DEST_DIR = "/var/www/html/"
    }

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/your-repo.git'
            }
        }

        stage('Deploy HTML using RSYNC') {
            steps {

                sshagent(['ec2-ssh-key']) {

                    sh '''
                    rsync -avz --delete \
                    -e "ssh -o StrictHostKeyChecking=no" \
                    $SOURCE_DIR \
                    $EC2_USER@$EC2_HOST:$DEST_DIR
                    '''
                }
            }
        }

        stage('Restart Nginx') {
            steps {

                sshagent(['ec2-ssh-key']) {

                    sh '''
                    ssh -o StrictHostKeyChecking=no \
                    $EC2_USER@$EC2_HOST "

                    sudo systemctl restart nginx

                    "
                    '''
                }
            }
        }
    }

    post {

        success {
            echo "HTML Deployment Successful"
        }

        failure {
            echo "Deployment Failed"
        }
    }
}
