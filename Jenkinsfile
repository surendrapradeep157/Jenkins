pipeline {

    agent {
        docker {
            image 'docker:28-cli'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        IMAGE_NAME = "python-web-app"
        CONTAINER_NAME = "python-web-app"
        PORT = "5000"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Version') {
            steps {
                sh 'docker version'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Remove Old Container') {
            steps {
                sh '''
                docker rm -f ${CONTAINER_NAME} || true
                '''
            }
        }

        stage('Run Container') {
            steps {
                sh """
                docker run -d \
                  --name ${CONTAINER_NAME} \
                  -p ${PORT}:5000 \
                  ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                sleep 5
                curl http://host.docker.internal:5000 || curl http://localhost:5000
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment Successful!"
        }

        failure {
            echo "Deployment Failed!"
        }
    }
}
