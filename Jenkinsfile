pipeline {
    agent any

    options {
        skipDefaultCheckout true // Prevents Jenkins from performing the default SCM checkout
    }

    stages {
        stage('Checkout') {
            steps {
                git(
                    branch: 'main',
                    url: 'git@github.com:Andreas0x07/docker-app.git',
                    credentialsId: 'github-ssh-key' // Reference to SSH credentials in Jenkins
                )
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("Andreas0x07/docker-app:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy Application') {
            steps {
                script {
                    sh '''
                        docker stop my-flask-app || true
                        docker rm my-flask-app || true
                        docker run -d --name my-flask-app -p 8081:80 Andreas0x07/docker-app:${BUILD_NUMBER}
                    '''
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

