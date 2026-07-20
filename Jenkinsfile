pipeline {
    agent any

    environment {
        DOCKER_IMAGE     = "nginx:alpine"
        MUMBAI_DOCKER_HOST = "tcp://3.110.123.71:2375" 
    }

    stages {
        stage('Pull Image on Mumbai VM') {
            steps {
                echo "Pulling Nginx image directly onto Mumbai VM Docker engine..."
                sh "docker -H ${MUMBAI_DOCKER_HOST} pull ${DOCKER_IMAGE}"
            }
        }

        stage('Deploy Exactly 5 Containers') {
            steps {
                script {
                    // This deploys exactly 5 containers on ports 8081 to 8085
                    for (int i = 1; i <= 5; i++) {
                        def containerName = "nginx-server-${i}"
                        def hostPort = 8080 + i 

                        echo "Managing ${containerName} on Mumbai VM..."
                        sh "docker -H ${MUMBAI_DOCKER_HOST} stop ${containerName} || true"
                        sh "docker -H ${MUMBAI_DOCKER_HOST} rm ${containerName} || true"
                        sh "docker -H ${MUMBAI_DOCKER_HOST} run -d --name ${containerName} -p ${hostPort}:80 ${DOCKER_IMAGE}"
                    }
                }
            }
        }

        stage('Clean Leftover Old Containers') {
            steps {
                script {
                    echo "Removing any older containers beyond number 5..."
                    // Clears out numbers 6, 7, 8, 9, 10 if they exist from old runs
                    for (int i = 6; i <= 10; i++) {
                        sh "docker -H ${MUMBAI_DOCKER_HOST} stop nginx-server-${i} || true"
                        sh "docker -H ${MUMBAI_DOCKER_HOST} rm nginx-server-${i} || true"
                    }
                }
            }
        }
    }
}
