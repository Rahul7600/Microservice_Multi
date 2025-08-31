pipeline {
    agent any

    environment {
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')
        PROJECT_ID = 'kubernetes-470606'
        CLUSTER_NAME = 'ecommerce'
        CLUSTER_ZONE = 'asia-south1-a'
    }

    stages {
        stage('Authenticate Docker to GCP') {
            steps {
                script {
                    sh "gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}"
                    sh "gcloud auth configure-docker asia-south1-docker.pkg.dev"
                }
            }
        }

        stage('Build & Tag Docker Image') {
            steps {
                script {
                    sh "docker build -t asia-south1-docker.pkg.dev/${PROJECT_ID}/repos/emailservice:latest ."
                }
            }
        }

        stage('Push Docker Image to GCP Artifacts') {
            steps {
                script {
                    sh "docker push asia-south1-docker.pkg.dev/${PROJECT_ID}/repos/emailservice:latest"
                }
            }
        }

        stage('Deploy to GKE') {
            steps {
                script {
                    sh "gcloud container clusters get-credentials ${CLUSTER_NAME} --zone ${CLUSTER_ZONE} --project ${PROJECT_ID}"
                    sh "kubectl apply -f emailservice.yaml"
                }
            }
        }
    }
}
