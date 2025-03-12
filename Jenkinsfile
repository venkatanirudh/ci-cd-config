pipeline {
    agent any
    environment {
        ARGOCD_SERVER = 'localhost:32095'  // Update to your ArgoCD server address
    }
    stages {
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
                        echo $JFROG_TOKEN | docker login perry2011.jfrog.io -u <your-email> --password-stdin
                        docker tag my-python-app:latest perry2011.jfrog.io/docker-repo/my-python-app:latest
                        docker push perry2011.jfrog.io/docker-repo/my-python-app:latest
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