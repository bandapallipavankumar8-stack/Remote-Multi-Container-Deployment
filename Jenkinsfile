pipeline {
    agent any

    environment {
        DOCKER_IMAGE     = "nginx:alpine"
        // Replace with your Mumbai VM Public IP
        MUMBAI_DOCKER_HOST = "tcp://3.110.123.71:2375" 
    }

    stages {
        stage('Pull Image on Mumbai VM') {
            steps {
                echo "Pulling Nginx image directly onto Mumbai VM Docker engine..."
                // The -H flag forces the command to execute on the remote machine
                sh "docker -H ${MUMBAI_DOCKER_HOST} pull ${DOCKER_IMAGE}"
            }
        }

        stage('Deploy 3 Containers to Mumbai') {
            steps {
                script {
                    for (int i = 1; i <=5; i++) {
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
    }
}
