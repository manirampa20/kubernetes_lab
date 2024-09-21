pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = 'dockerhub-credentials' // Use the credentials ID from Jenkins
        DOCKER_IMAGE = 'dockerdemo'
        DOCKER_REGISTRY='docker.io'

    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Cloning repository...'
                git branch: 'main', url: 'https://github.com/manirampa20/CI_CD_Pipeline_Jenkins_lab.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the application...'
                bat 'mvn clean package'  // Build the project
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                bat 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    try {
                        // Build Docker image and tag it with the Jenkins build number
                        bat 'docker build -t manirampa20/dockerdemo-app:latest .'
                        echo 'Docker Image Built successfully'
                    } catch (Exception e) {
                        error "Failed to build Docker Image: ${e.message}"
                    }
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                script {
                    try {
                        echo 'Pushing Docker image to Docker Hub...'
                        withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS, usernameVariable: 'DOCKER_CREDENTIALS_USR', passwordVariable: 'DOCKER_CREDENTIALS_PSW')]) {
                            bat '''
                               docker login -u %DOCKER_CREDENTIALS_USR% -p %DOCKER_CREDENTIALS_PSW%
                               docker tag manirampa20/dockerdemo-app:latest manirampa20/dockerdemo-app:latest
                               docker push manirampa20/dockerdemo-app:latest
                            '''
                        }
                        echo 'Deployment completed successfully'
                    } catch (Exception e) {
                        error "Deployment failed: ${e.message}"
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        success {
            echo 'Pipeline succeeded.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
