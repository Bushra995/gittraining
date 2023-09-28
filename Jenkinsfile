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
                // Add your build steps here
                echo "Building for ${params.ENVIRONMENT} environment"
            }
        }

        stage('Retrieve Author Email') {
            steps {
                script {
                    // Use 'git log' command to get the author's email
                    def authorEmail = sh(script: 'git log -1 --pretty=format:%ae', returnStdout: true).trim()
                    
                    // Print the author's email to the console
                    echo "Author's Email: ${authorEmail}"
                    
                    // Store the author's email in an environment variable for later use
                    env.AUTHOR_EMAIL = authorEmail
                }
            }
        }

        stage('Deploy') {
            when {
                expression {
                    // You can use the 'params.ENVIRONMENT' variable to make decisions in your pipeline
                    return params.ENVIRONMENT == 'qa' || params.ENVIRONMENT == 'prod'
                }
            }
            steps {
                // Add your deployment steps here
                echo "Deploying to ${params.ENVIRONMENT} environment"
                
                // You can access the author's email using 'env.AUTHOR_EMAIL' here
                echo "Author's Email during deployment: ${env.AUTHOR_EMAIL}"
            }
        }

        stage('Test') {
            steps {
                // Add your testing steps here
                echo "Testing in ${params.ENVIRONMENT} environment"
            }
        }
    }
    
    post {
        always {
            // Add any cleanup or post-build steps here
            echo "Pipeline completed for ${params.ENVIRONMENT} environment"
        }
        success {
            // Send an email notification on success to the author's email
            emailext subject: "Pipeline Successful for ${params.ENVIRONMENT} environment",
                      body: "The pipeline for ${params.ENVIRONMENT} environment has completed successfully.",
                      to: env.AUTHOR_EMAIL
        }
        failure {
            // Send an email notification on failure to the author's email
            emailext subject: "Pipeline Failed for ${params.ENVIRONMENT} environment",
                      body: "The pipeline for ${params.ENVIRONMENT} environment has failed. Please investigate.",
                      to: env.AUTHOR_EMAIL
        }
    }
}
