pipeline {
    agent any

    environment {
        DOCKER_IMAGE       = "nginx:alpine"
        MUMBAI_DOCKER_HOST = "tcp://13.233.118.191:2375" 
    }

    stages {
        stage('Pull Image on Mumbai VM') {
            steps {
                echo "Pulling latest Nginx image on Mumbai VM..."
                sh "docker -H ${MUMBAI_DOCKER_HOST} pull ${DOCKER_IMAGE}"
            }
        }

        stage('Deploy 5 Containers from Root Folder') {
            steps {
                script {
                    for (int i = 1; i <= 3; i++) {
                        def containerName = "nginx-server-${i}"
                        def hostPort = 8080 + i 

                        echo "Cleaning old instance for ${containerName}..."
                        sh "docker -H ${MUMBAI_DOCKER_HOST} stop ${containerName} || true"
                        sh "docker -H ${MUMBAI_DOCKER_HOST} rm ${containerName} || true"
                        
                        echo "Launching ${containerName} on port ${hostPort}..."
                        sh "docker -H ${MUMBAI_DOCKER_HOST} run -d --name ${containerName} -p ${hostPort}:80 ${DOCKER_IMAGE}"
                        
                        echo "Copying website contents from root into ${containerName}..."
                        // Changed 'src/.' to '.' because your files are in the main root directory
                        sh "docker -H ${MUMBAI_DOCKER_HOST} cp . ${containerName}:/usr/share/nginx/html/"
                    }
                }
            }
        }

        stage('Clean Leftover Old Containers') {
            steps {
                script {
                    echo "Removing any older containers beyond number 5..."
                    for (int i = 6; i <= 10; i++) {
                        sh "docker -H ${MUMBAI_DOCKER_HOST} stop nginx-server-${i} || true"
                        sh "docker -H ${MUMBAI_DOCKER_HOST} rm nginx-server-${i} || true"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Successfully deployed all 5 website instances to your Mumbai VM!"
        }
    }
}
