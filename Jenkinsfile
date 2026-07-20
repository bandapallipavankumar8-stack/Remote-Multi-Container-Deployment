pipeline {
    agent any

    environment {
        DOCKER_IMAGE    = "nginx:alpine"
        // Replace this with your actual Remote VM IP address
        REMOTE_VM_IP    = "192.168.1.50" 
        // Must match the ID you created in Jenkins Credentials
        SSH_CRED_ID     = "remote-vm-ssh-key" 
    }

    stages {
        stage('Pull Image on Remote VM') {
            steps {
                // sshagent provides secure SSH keys inside this block
                sshagent([env.SSH_CRED_ID]) {
                    echo "Pulling Nginx image on the remote VM..."
                    sh "ssh -o StrictHostKeyChecking=no target-user@${REMOTE_VM_IP} 'docker pull ${DOCKER_IMAGE}'"
                }
            }
        }

        stage('Clean and Deploy 3 Containers Remotely') {
            steps {
                sshagent([env.SSH_CRED_ID]) {
                    script {
                        // Loop to create 3 containers: index 1, 2, and 3
                        for (int i = 1; i <= 3; i++) {
                            def containerName = "nginx-server-${i}"
                            // This creates unique ports: 8081, 8082, 8083
                            def hostPort = 8080 + i 

                            echo "Managing ${containerName} on port ${hostPort}..."
                            
                            // Stop and remove existing container if it exists on remote VM
                            sh "ssh -o StrictHostKeyChecking=no target-user@${REMOTE_VM_IP} 'docker stop ${containerName} || true'"
                            sh "ssh -o StrictHostKeyChecking=no target-user@${REMOTE_VM_IP} 'docker rm ${containerName} || true'"
                            
                            // Start the new container on the remote VM
                            sh "ssh -o StrictHostKeyChecking=no target-user@${REMOTE_VM_IP} 'docker run -d --name ${containerName} -p ${hostPort}:80 ${DOCKER_IMAGE}'"
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Successfully deployed 3 containers to remote VM (${REMOTE_VM_IP}) on ports 8081, 8082, and 8083!"
        }
    }
}
