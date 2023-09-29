pipeline {
    agent any

    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'qa', 'prod'],
            description: 'Select the deployment environment'
        )
    }

    stages {
        stage('Build') {
            steps {
                echo "Building for ${params.ENVIRONMENT} environment"
            }
        }

        stage('Retrieve Author Email') {
            steps {
                script {
                    def authorEmail = sh(script: 'git log -1 --pretty=format:%ae', returnStdout: true).trim()
                    echo "Author's Email: ${authorEmail}"
                    env.AUTHOR_EMAIL = authorEmail
                }
            }
        }

        stage('Deploy') {
            when {
                expression {
                    return params.ENVIRONMENT == 'qa' || params.ENVIRONMENT == 'prod'
                }
            }
            steps {
                echo "Deploying to ${params.ENVIRONMENT} environment"
                echo "Author's Email during deployment: ${env.AUTHOR_EMAIL}"
            }
        }

        stage('Test') {
            steps {
                echo "Testing in ${params.ENVIRONMENT} environment"
            }
        }

        stage('Interactive input') {
            steps {
                input(message: 'Proceed with the next step?', submitter: 'user', parameters: [
                    booleanParam(defaultValue: true, description: 'Do you want to continue?', name: 'CONTINUE')
                ])
                script {
                    if (params.CONTINUE) {
                        echo "Continuing with the next step..."
                        // Add your steps here
                    } 
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline completed for ${params.ENVIRONMENT} environment"
        }
        success {
            emailext subject: "Pipeline Successful for ${params.ENVIRONMENT} environment",
                      body: "The pipeline for ${params.ENVIRONMENT} environment has completed successfully.",
                      to: "bushraf995@gmail.com"
        }
        failure {
            emailext subject: "Pipeline Failed for ${params.ENVIRONMENT} environment",
                      body: "The pipeline for ${params.ENVIRONMENT} environment has failed. Please investigate.",
                      to: env.AUTHOR_EMAIL
        }
    }
}
