pipeline {
    agent any

    environment {
        IMAGE_NAME = 'yourdockerhubusername/my-web-app'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/YOUR_GITHUB_USERNAME/my-web-app.git'
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 8080:80 $IMAGE_NAME'
            }
        }
    }
}
