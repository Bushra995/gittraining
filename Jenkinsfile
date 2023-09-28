def githubToken = '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' // Replace with your GitHub PAT

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
                // Build steps go here
                echo "Building for ${params.ENVIRONMENT} environment"
                sh 'mvn clean install'  // Replace with your build commands
            }
        }

        stage('Test') {
            steps {
                script {
                    if (params.ENVIRONMENT == 'dev') {
                        echo "Skipping tests for the 'dev' environment"
                    } else {
                        echo "Running tests in ${params.ENVIRONMENT} environment"
                        // Add test execution steps
                    }
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
                script {
                    if (params.ENVIRONMENT == 'qa') {
                        echo "Deploying to QA environment"
                        // Add deployment steps for QA
                    } else if (params.ENVIRONMENT == 'prod') {
                        echo "Deploying to Production environment"
                        // Add deployment steps for production
                    }
                }
            }
        }
    }

    post {
        always {
            // Cleanup or post-build steps go here
            echo "Pipeline completed for ${params.ENVIRONMENT} environment"
        }
        success {
            script {
                def githubUser = sh(script: "curl -s -H 'Authorization: token ${githubToken}' https://api.github.com/user", returnStdout: true).trim()
                def githubEmail = sh(script: "curl -s -H 'Authorization: token ${githubToken}' https://api.github.com/user/emails", returnStdout: true).trim()
                def recipientEmail = ""
                
                if (githubUser.contains('email')) {
                    def emailJson = readJSON text: githubEmail
                    recipientEmail = emailJson[0].email
                }
                
                emailext subject: "Pipeline Successful for ${params.ENVIRONMENT} environment",
                          body: "The pipeine for ${params.ENVIRONMENT} environment has completed successfully.",
                          to: recipientEmail
            }
        }
        failure {
            script {
                def githubUser = sh(script: "curl -s -H 'Authorization: token ${githubToken}' https://api.github.com/user", returnStdout: true).trim()
                def githubEmail = sh(script: "curl -s -H 'Authorization: token ${githubToken}' https://api.github.com/user/emails", returnStdout: true).trim()
                def recipientEmail = ""
                
                if (githubUser.contains('email')) {
                    def emailJson = readJSON text: githubEmail
                    recipientEmail = emailJson[0].email
                }
                
                emailext subject: "Pipeline Failed for ${params.ENVIRONMENT} environment",
                          body: "The pipeline for ${params.ENVIRONMENT} environment has failed. Please investigate.",
                          to: recipientEmail
            }
        }
    }
}
