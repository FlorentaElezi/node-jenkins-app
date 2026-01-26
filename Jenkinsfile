pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        IMAGE_NAME = "yourdockerhubusername/node-jenkins-app"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Build') {
            steps {
                echo 'Installing Node.js dependencies...'
                bat 'npm install'
            }
        }

        /*stage('Test') {
            steps {
                echo 'Running a basic test...'
                bat 'node -e "require(\'./app.js\')"'
            }
        }*/

        stage('Test') {
    steps {
        echo 'Running app start test (10 seconds)...'
        bat '''
        start "" cmd /c "node app.js"
        timeout /t 10
        taskkill /IM node.exe /F
        '''
    }
}

        stage('Docker Build & Push') {
            steps {
                echo 'Building Docker image...'
                bat "docker build -t %IMAGE_NAME%:%IMAGE_TAG% ."

                echo 'Logging into Docker Hub...'
                bat "docker login -u %DOCKERHUB_CREDENTIALS_USR% -p %DOCKERHUB_CREDENTIALS_PSW%"

                echo 'Pushing Docker image...'
                bat "docker push %IMAGE_NAME%:%IMAGE_TAG%"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying to Kubernetes...'
                bat "kubectl apply -f k8s\\deployment.yaml"
                bat "kubectl apply -f k8s\\service.yaml"
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}
