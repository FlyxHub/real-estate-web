pipeline {
    agent {
        label 'AppServer' // Define a specific agent node label
    }

    environment {
        DOCKER_IMAGE = 'flyxdocker/real-estate-web'
        DOCKER_REGISTRY = 'https://registry.hub.docker.com'
        DOCKER_CREDENTIALS_ID = 'dockerhub-creds'
    }

    stages {
        stage('Checkout') {
            steps {
                // Use 'checkout scm' to clone the repository
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image using a specific Docker context (if necessary)
                    app = docker.build("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    // Push the image to DockerHub with versioning and tagging best practices
                    docker.withRegistry("${DOCKER_REGISTRY}", "${DOCKER_CREDENTIALS_ID}") {
                        app.push("${env.BUILD_NUMBER}")
                        app.push('latest') // Also push the 'latest' tag if needed
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Safely bring down any existing containers before deploying the new version
                    sh 'docker-compose down'
                    sh 'docker-compose up -d'
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace after pipeline execution
            cleanWs()
        }
        success {
            echo "Deployment successful!"
        }
        failure {
            echo "Deployment failed."
        }
    }
}
