pipeline {
    agent any

    environment {
        IMAGE_NAME = "localhost:5000/world-clock"
        TAG        = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    echo "=== Running tests ==="
                    chmod +x test.sh
                    ./test.sh
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "=== Building Docker image ==="
                    docker build -t $IMAGE_NAME:$TAG .
                '''
            }
        }

        stage('Approval Before Push') {
            steps {
                input message: 'Approve pushing Docker image to local registry?',
                      ok: 'Push Image'
            }
        }

        stage('Push Image to Registry') {
            steps {
                sh '''
                    echo "=== Pushing image to local registry ==="
                    docker push $IMAGE_NAME:$TAG
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Build and push completed successfully"
        }
        failure {
            echo "❌ Build failed"
        }
        aborted {
            echo "⚠️ Build aborted"
        }
    }
}
