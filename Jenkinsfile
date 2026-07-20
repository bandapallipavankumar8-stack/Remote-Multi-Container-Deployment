pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "nginx:alpine"
        MUMBAI_DOCKER_HOST = "tcp://3.110.123.71:2375" 
    }
    stages {
        stage('Deploy 5 Containers to Mumbai') {
            steps {
                script {
                    for (int i = 1; i <= 5; i++) {
                        def containerName = "nginx-server-${i}"
                        def hostPort = 8080 + i 
                        sh "docker -H ${MUMBAI_DOCKER_HOST} stop ${containerName} || true"
                        sh "docker -H ${MUMBAI_DOCKER_HOST} rm ${containerName} || true"
                        sh "docker -H ${MUMBAI_DOCKER_HOST} run -d --name ${containerName} -p ${hostPort}:80 ${DOCKER_IMAGE}"
                    }
                }
            }
        }
    }
}
