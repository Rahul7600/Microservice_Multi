pipeline {
    agent any

    environment {
        // Jenkins secret file credential for GCP Service Account JSON key
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')
        PROJECT_ID = "kubernetes-470606"
        CLUSTER_NAME = "gke-ecommerce"       // <-- Replace with your GKE cluster name
        CLUSTER_ZONE = "asia-south1-a"       // <-- Replace with your cluster's zone
        IMAGE = "asia-south1-docker.pkg.dev/kubernetes-470606/repos/shippingservice:latest"
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
                    // Apply YAML directly from project root (no k8s/ folder)
                    sh "kubectl apply -f shippingservice.yaml"
                }
            }
        }
    }
}
