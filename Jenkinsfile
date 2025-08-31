pipeline {
    agent any

    environment {
        // Jenkins secret for GCP service account JSON key
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')
        PROJECT_ID = "kubernetes-470606"
        CLUSTER_NAME = "gke-ecommerce"      // <-- Change if your cluster name differs
        CLUSTER_ZONE = "asia-south1-a"      // <-- Change if your zone differs
        IMAGE = "asia-south1-docker.pkg.dev/kubernetes-470606/repos/paymentservice:latest"
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
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker build -t ${IMAGE} ."
                    }
                }
            }
        }
        
        stage('Push Docker Image to GCP Artifacts') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push ${IMAGE}"
                    }
                }
            }
        }

        stage('Authenticate kubectl with GKE') {
            steps {
                script {
                    sh "gcloud container clusters get-credentials ${CLUSTER_NAME} --zone ${CLUSTER_ZONE} --project ${PROJECT_ID}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl apply -f paymentservice.yaml"
                }
            }
        }
    }
}
