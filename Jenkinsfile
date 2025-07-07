pipeline {
    agent any

    environment {
        // Your Docker Hub image name
        IMAGE_NAME = 'sahanadocker10/mywebapp'
    }

    stages {
        stage('Clone GitHub Repository') {
            steps {
                // Pulls code from your GitHub repo
                git 'https://github.com/Sahana1110/mywebapp.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Builds Docker image from Dockerfile in repo
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                // Authenticates and pushes image to Docker Hub
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        echo $PASSWORD | docker login -u $USERNAME --password-stdin
                        docker push $IMAGE_NAME
                    '''
                }
            }
        }

        stage('Stop Old Containers') {
            steps {
                // Stops any running containers with the same image
                sh '''
                    docker ps -q --filter ancestor=$IMAGE_NAME | xargs -r docker stop
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                // Runs container on port 8080 (accessible in browser)
                sh 'docker run -d -p 8081:80 $IMAGE_NAME'
            }
        }
    }
}
