pipeline {
    agent any

    environment {
        // Reference the Jenkins secret file for GCP authentication
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')
    }

    stages {
        stage('Authenticate Docker to GCP') {
            steps {
                script {
                    // Authenticate Docker with GCP Artifact Registry
                    sh "gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}"
                    sh "gcloud auth configure-docker asia-south1-docker.pkg.dev"
                }
            }
        }

        stage('Build & Tag Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker build -t asia-south1-docker.pkg.dev/kubernetes-470606/repos/shippingservice:latest ."
                    }
                }
            }
        }
        
        stage('Push Docker Image to GCP Artifacts') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push asia-south1-docker.pkg.dev/kubernetes-470606/repos/shippingservice:latest"
                    }
                }
            }
        }
    }
}
