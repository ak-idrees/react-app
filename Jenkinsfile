pipeline {
    agent any

    parameters {
        choice(
            name: 'CLIENT',
            choices: ['sigma', 'roko'],
            description: 'Select the client to build Docker image for'
        )
    }

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials-id'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                // This clones your repo into the workspace
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test -- --watch=false || true'
            }
        }

        stage('Build React App') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (params.CLIENT == 'sigma') {
                        env.IMAGE_NAME = 'wimetirix/sgima-app'
                    } else if (params.CLIENT == 'roko') {
                        env.IMAGE_NAME = 'wimetirix/roko-app'
                    } else {
                        error 'Invalid client selected'
                    }

                    sh "docker build -t ${env.IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: DOCKERHUB_CREDENTIALS,
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh "docker push ${env.IMAGE_NAME}:${IMAGE_TAG}"
                    sh 'docker logout'
                }
            }
        }
    }
}
