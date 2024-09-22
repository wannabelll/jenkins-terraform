pipeline {
    agent any

    parameters {
        choice(name: 'ACTION', choices: ['plan', 'apply', 'destroy'], description: 'Choose the action to perform with Terraform.')
    }

    stages {
        stage('Set Default Action') {
            steps {
                script {
                    // Check if the ACTION parameter is empty, set default if so
                    if (!params.ACTION) {
                        params.ACTION = 'plan' // Set default action
                    }
                }
            }
        }
        stage('Checkout') {
            steps {
                git branch: 'master', credentialsId: 'ssh-private-key-v1.0', url: 'git@github.com:wannabelll/Terraform_pet.git'
            }
        }
        stage('Terraform init') {
            steps {
                sh 'terraform init'
            }
        }
        stage('Terraform Action') {
            steps {
                script {
                    // Use withCredentials to inject AWS credentials
                    withCredentials([string(credentialsId: 'aws-access-key-id', variable: 'AWS_ACCESS_KEY_ID'),
                                     string(credentialsId: 'aws-secret-access-key', variable: 'AWS_SECRET_ACCESS_KEY')]) {
                        // Now the AWS credentials are available as environment variables
                        if (params.ACTION == 'plan') {
                            sh 'terraform plan'
                        } else if (params.ACTION == 'apply') {
                            sh 'terraform apply --auto-approve'
                        } else if (params.ACTION == 'destroy') {
                            sh 'terraform destroy --auto-approve'
                        }
                    }
                }
            }
        }
    }
}
