pipeline {
    agent any

    environment {
        NODE_VERSION = "18"
        DOCKERHUB_USERNAME = credentials('DOCKERHUB_USERNAME')
        DOCKERHUB_PASS = credentials('DOCKERHUB_PASS')
        GITHUB_TOKEN = credentials('GITHUB_TOKEN') // Personal Access Token
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/ak-idrees/react-app.git', branch: 'main'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build React App') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                sh """
                docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASS
                docker build -t $DOCKERHUB_USERNAME/simple-reactjs-app:${GIT_COMMIT} .
                docker push $DOCKERHUB_USERNAME/simple-reactjs-app:${GIT_COMMIT}
                """
            }
        }

        stage('Trigger GitHub Action (CD)') {
            steps {
                sh """
                curl -X POST \
                -H "Accept: application/vnd.github+json" \
                -H "Authorization: token $GITHUB_TOKEN" \
                https://api.github.com/repos/ak-idrees/react-app/dispatches \
                -d '{"event_type":"jenkins_build_complete"}'
                """
            }
        }
    }
}

