pipeline {
    agent any

    environment {
        SERVER_USER = "shubham"
        SERVER_IP   = "172.21.2.70"        
        APP_DIR     = "/home/shubham/flipcart-ui"
        SSH_KEY     = credentials('ssh_key') // Jenkins Credential ID
        REPO_URL    = "git@github.com:shu11ham/flipcart_ui.git"
        IMAGE_NAME  = "flipcart_ui-web"
        CONTAINER_NAME = "flipcart_ui-web"
        PORT        = "8090:80"
    }

    stages {

        stage('Deploy to Server') {
            steps {
                script {
                    // SSH into server and deploy
                    sh """
                    ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_IP} '
                        echo "Deploying flipcart-ui..."
                        
                        # Clone repo if not exists
                        if [ ! -d "${APP_DIR}" ]; then
                            git clone ${REPO_URL} ${APP_DIR}
                        fi
                        
                        cd ${APP_DIR}
                        
                        # Pull latest code
                        git fetch --all
                        git reset --hard origin/main
                        
                        # Stop and remove old container
                        docker rm -f ${CONTAINER_NAME} || true
                        
                        # Build new Docker image
                        docker build -t ${IMAGE_NAME} .
                        
                        # Run container
                        docker run -d --name ${CONTAINER_NAME} -p ${PORT} ${IMAGE_NAME}
                        
                        echo "Deployment completed!"
                    '
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully ✅"
        }
        failure {
            echo "Pipeline failed ❌"
        }
    }
}
