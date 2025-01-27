pipeline {
    agent any

    environment {
        GIT_CRED = 'github-token-id' // Jenkins credentials ID for GitHub token
        GIT_REPO = 'https://github.com/umar-fayaaz/micro_service.git' // GitHub repository URL
        DOCKER_NETWORK = 'umar' // Custom Docker network name
    }

    stages {
        stage('Checkout Code from GitHub') {
            steps {
                script {
                    // Checkout code from GitHub using Jenkins credentials
                    checkout([$class: 'GitSCM', 
                              branches: [[name: '*/master']], 
                              userRemoteConfigs: [[url: "${GIT_REPO}", credentialsId: "${GIT_CRED}"]]
                    ])
                }
            }
        }

        stage('Create Docker Network') {
            steps {
                script {
                    // Check if the Docker network exists and create if it doesn't
                    def networkExists = sh(script: "docker network ls | grep -w ${DOCKER_NETWORK}", returnStatus: true) == 0
                    if (!networkExists) {
                        sh "docker network create ${DOCKER_NETWORK}"
                        echo "Docker network '${DOCKER_NETWORK}' created."
                    } else {
                        echo "Docker network '${DOCKER_NETWORK}' already exists."
                    }
                }
            }
        }

        stage('Build and Run Welcome Service') {
            steps {
                script {
                    // Build Docker image for the welcome-service
                    sh "docker build -t welcome-service-image ./welcome-service"

                    // Run the welcome-service container
                    sh "docker run -d --name welcome-service --network ${DOCKER_NETWORK} -p 8081:8081 welcome-service-image"
                }
            }
        }

        stage('Build and Run User Service') {
            steps {
                script {
                    // Build Docker image for the user-service
                    sh "docker build -t user-service-image ./user-service"

                    // Run the user-service container
                    sh "docker run -d --name user-service --network ${DOCKER_NETWORK} -p 8082:8082 user-service-image"
                }
            }
        }

        stage('Build and Run Auth Service') {
            steps {
                script {
                    // Build Docker image for the auth-service
                    sh "docker build -t auth-service-image ./auth-service"

                    // Run the auth-service container
                    sh "docker run -d --name auth-service --network ${DOCKER_NETWORK} -p 8083:8083 auth-service-image"
                }
            }
        }
    }
}
