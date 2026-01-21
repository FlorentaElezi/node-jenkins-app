pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds') // Add this in Jenkins
        IMAGE_NAME = "yourdockerhubusername/node-jenkins-app"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Build') {
            steps {
                echo 'Installing Node.js dependencies...'
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running a simple test...'
                sh 'node -e "require(\'./app.js\')"'
            }
        }

        stage('Docker Build & Push') {
            steps {
                echo 'Building and pushing Docker image...'
                sh """
                    docker build -t $IMAGE_NAME:$IMAGE_TAG .
                    docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW
                    docker push $IMAGE_NAME:$IMAGE_TAG
                """
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying to Kubernetes...'
                sh """
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                """
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}
