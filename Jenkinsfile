pipeline {
    agent any

    environment {
        IMAGE_NAME = 'sahanadocker10/mywebapp:latest'
        KUBECONFIG = '/var/lib/jenkins/.kube/config'  // Adjust if your kube config is elsewhere
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

        stage('Update Kubernetes YAMLs') {
            steps {
                script {
                    // Replace old image in deployment.yaml with new Docker image
                    sh '''
                        sed -i "s|image: .*|image: ${IMAGE_NAME}|g" deployment.yaml
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_PATH')]) {
                    sh '''
                        export KUBECONFIG=$KUBECONFIG_PATH
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_PATH')]) {
                    sh '''
                        export KUBECONFIG=$KUBECONFIG_PATH
                        kubectl get pods -o wide
                    '''
                }
            }
        }
    }
}
