pipeline {
    agent any

    options {
        skipDefaultCheckout true
    }

    stages {
        stage('Checkout') {
            steps {
                git(
                    branch: 'main',
                    url: 'git@github.com:andreas0x07/docker-app.git',
                    credentialsId: 'github-ssh-key'
                )
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def dockerImage = docker.build("andreas0x07/docker-app:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    def dockerImage = docker.image("andreas0x07/docker-app:${env.BUILD_NUMBER}")

                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy Application') {
            steps {
                script {
                    sh """
                        docker stop my-flask-app || true
                        docker rm my-flask-app || true
                        docker run -d --name my-flask-app -p 8081:80 andreas0x07/docker-app:${env.BUILD_NUMBER}
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo "Pipeline completed successfully."
        }
        failure {
            echo "Pipeline failed."
        }
    }
}
