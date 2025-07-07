pipeline {
    agent any

    environment {
        IMAGE_NAME = 'sahanadocker10/mywebapp'
    }

    stages {
        stage('Clone GitHub Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Sahana1110/mywebapp.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
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
                sh '''
                    docker ps -q --filter ancestor=$IMAGE_NAME | xargs -r docker stop
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                    # Stop and remove any container using port 8081
                    docker ps --filter "publish=8081" -q | xargs -r docker rm -f

                    # Run the new container
                    docker run -d -p 8081:80 $IMAGE_NAME
                '''
            }
        }
    }
}

