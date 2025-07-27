pipeline {
    agent any

    environment {
        IMAGE_NAME = "webapp:${GIT_COMMIT}"
        DOCKER_HOST = "ubuntu@172.31.2.193"
    }

    stages {
        stage('Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Test') {
            steps {
                sh 'echo "Test passed (mock test)"'
            }
        }

        stage('Deploy to Prod') {
            when {
                branch 'master'
            }
            steps {
                sshagent(['docker-host-ssh']) {
                    sh """
                    docker save $IMAGE_NAME | ssh -o StrictHostKeyChecking=no $DOCKER_HOST 'docker load'
                    ssh $DOCKER_HOST 'docker rm -f webapp || true && docker run -d --name webapp -p 80:80 $IMAGE_NAME'
                    """
                }
            }
        }
    }
}
