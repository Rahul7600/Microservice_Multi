pipeline {
    agent any

    parameters {
        choice(
            name: 'SERVICE',
            choices: [
                'adservice',
                'cartservice',
                'checkoutservice',
                'currencyservice',
                'emailsservice',
                'frontend',
                'loadgenerator',
                'main',
                'paymentservice',
                'productcatalogservice',
                'recommendationservice',
                'shippingservice'
            ],
            description: 'Choose the service to deploy'
        )
    }

    stages {
        stage('Select Service') {
            steps {
                script {
                    echo "Selected service: ${params.SERVICE}"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Example of deploying or building the selected service
                    echo "Deploying the ${params.SERVICE} service..."
                    // For example, you can call different scripts or Docker commands based on the selected service:
                    // sh "./deploy ${params.SERVICE}"
                }
            }
        }
    }
}
