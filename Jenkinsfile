pipeline {
    agent any
    environment {
        ARGOCD_SERVER = 'localhost:30392'  // Update to your ArgoCD server address
    }
    stages {
        stage('Debug Environment') {
            steps {
                sh 'echo $PATH'
            }
        }
        stage('Check Docker') {
            steps {
                sh 'docker --version'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t my-python-app .'
            }
        }
        stage('Push to JFrog Artifactory') {
            steps {
                withCredentials([string(credentialsId: 'artifactory-token', variable: 'JFROG_TOKEN')]) {
                    sh '''
                        echo $JFROG_TOKEN | docker login trial5tk4k8.jfrog.io -u venkatanirudh.pillala@gmail.com --password-stdin
                        docker tag my-python-app:latest trial5tk4k8.jfrog.io/docker-repo/my-python-app:latest
                        docker push trial5tk4k8.jfrog.io/docker-repo/my-python-app:latest
                    '''
                }
            }
        }
        stage('Deploy with ArgoCD') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'argocd-credentials', usernameVariable: 'ARGOCD_USER', passwordVariable: 'ARGOCD_PASSWORD')]) {
                    sh '''
                        argocd login $ARGOCD_SERVER --username $ARGOCD_USER --password $ARGOCD_PASSWORD --insecure
                        argocd app sync my-python-app --server $ARGOCD_SERVER
                    '''
                }
            }
        }
    }
}
