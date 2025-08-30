pipeline {
    agent any

    stages {
        stage('Build & Tag Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker build -t asia-south1-docker.pkg.dev/kubernetes-470606/repos/checkoutservice:latest ."
                    }
                }
            }
        }
        
        stage('Docker Image Push to GCP Artifacts') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push asia-south1-docker.pkg.dev/kubernetes-470606/repos/checkoutservice:latest"
                    }
                }
            }
        }
    }
}
