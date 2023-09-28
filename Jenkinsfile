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

        stage('Interactive input') {
            steps {
                input(message: 'Proceed with the next step?', submitter: 'user', parameters: [
                    booleanParam(defaultValue: true, description: 'Do you want to continue?', name: 'CONTINUE')
                ])
                script {
                    if (params.CONTINUE) {
                        echo "Continuing with the next step..."
                        // Add your steps here
                    } else {
                        error("Pipeline aborted by the user.")
                    }
                }
            }
        }
            
 }

         stage('Create build output') {
            steps {
                script {
                    // Make the output directory.
                    sh "mkdir -p output"
                    // Write a useful file, which is needed to be archived.
                    writeFile file: "output/usefulfile.txt", text: "This file is useful, need to archive it."
                    // Write a useless file, which is not needed to be archived.
                    writeFile file: "output/uselessfile.md", text: "This file is useless, no need to archive it."
                }
            }
        }
        stage('Archive build output') {
            steps {
                // Archive the build output artifacts.
                archiveArtifacts artifacts: 'output/*.txt', excludes: 'output/*.md'
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
