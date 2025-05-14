pipeline {
    agent any

    environment {
        IMAGE_NAME = "jayram0407/google-map-api:latest"
        DOCKERHUB_CREDENTIALS = 'dockerhub-cred'    
        GITHUB_CREDENTIALS = 'github-cred'
        KUBECONFIG = '/root/.kube/config'             
    }

    stages {
        stage('Clone Repository') {
            steps {
                git credentialsId: "${GITHUB_CREDENTIALS}", url: 'https://github.com/jayram0402/Google-Map-API.SpringBoot.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $IMAGE_NAME
                    '''
                }
            }
        }

        stage('Apply K8s Manifests') {
            steps {
                sh 'kubectl apply -f k8s/'
            }
        }

        stage('Trigger ArgoCD Sync') {
            steps {
                sh 'argocd app sync google-map-api --insecure --grpc-web'
            }
        }
    }
}
