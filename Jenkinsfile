pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "princeekr/kuber-test"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_CONTENT')]) {
                    writeFile file: 'kubeconfig.yaml', text: KUBECONFIG_CONTENT
                    sh 'kubectl --kubeconfig=kubeconfig.yaml apply -f k8s-deployment.yaml'
                }
            }
        }
    }
}