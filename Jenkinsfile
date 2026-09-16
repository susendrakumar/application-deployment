pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'susesk/trend-app'
        AWS_REGION = 'ap-south-1'
        EKS_CLUSTER = 'trend-cluster'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
                sh 'docker tag $DOCKER_IMAGE:$BUILD_NUMBER $DOCKER_IMAGE:latest'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh 'docker push $DOCKER_IMAGE:$BUILD_NUMBER'
                    sh 'docker push $DOCKER_IMAGE:latest'
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh 'aws eks update-kubeconfig --region $AWS_REGION --name $EKS_CLUSTER'
                sh 'kubectl set image deployment/trend-app trend-app=$DOCKER_IMAGE:$BUILD_NUMBER'
                sh 'kubectl rollout status deployment/trend-app --timeout=180s'
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}