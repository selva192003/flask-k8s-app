pipeline {
    agent any

    environment {
        IMAGE_NAME = "selva192003/flask-ml-app"
        IMAGE_TAG = "latest"
        CHART_NAME = "flask-chart"
        CHART_PATH = "helm-chart/${CHART_NAME}"
        RELEASE_NAME = "flask-app"
        NAMESPACE = "default"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/your-repo/flask-k8s-app.git' // <-- replace if using Jenkins Git integration
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t $IMAGE_NAME:$IMAGE_TAG ./app"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $IMAGE_NAME:$IMAGE_TAG
                    """
                }
            }
        }

        stage('Deploy with Helm') {
            steps {
                script {
                    sh """
                        helm upgrade --install $RELEASE_NAME $CHART_PATH \
                            --set image.repository=$IMAGE_NAME \
                            --set image.tag=$IMAGE_TAG \
                            --namespace $NAMESPACE --create-namespace
                    """
                }
            }
        }
    }
}
