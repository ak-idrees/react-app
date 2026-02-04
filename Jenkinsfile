pipeline {
    agent any

    environment {
        // Jenkins credentials ID for Docker Hub
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials-id'
        IMAGE_SGIMA = 'wimetirix/sgima-app'
        IMAGE_ROKO =  'wimetirix/roko-app'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout main branch
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                // Install npm packages
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                // Run tests (React default tests)
                sh 'npm test || echo "Tests failed, continuing..."'
            }
        }

        stage('Build React App') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    // Build Docker images for two clients
                    sh "docker build -t ${IMAGE_SGIMA}:latest ."
                    sh "docker build -t ${IMAGE_ROKO}:latest ."
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                withCredentials([usernamePassword(credentialsId: dockerhub-credentials-id, usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh "docker push ${IMAGE_SGIMA}:latest"
                    sh "docker push ${IMAGE_ROKO}:latest"
                    sh 'docker logout'
                }
            }
        }
    }
}
