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
            script {
                // Get the Git commit hash for the latest commit
                def gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                
                // Get the author's email using 'git show' command
                def authorEmail = sh(returnStdout: true, script: "git show --format='%ae' -s ${gitCommit}").trim()
                
                // Send an email notification on success to the GitHub user who committed the code
                emailext subject: "Pipeline Successful for ${params.ENVIRONMENT} environment",
                          body: "The pipeline for ${params.ENVIRONMENT} environment has completed successfully.",
                          to: "${authorEmail}" // Send email to the author's email address
            }
        }
        failure {
            script {
                // Get the Git commit hash for the latest commit
                def gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                
                // Get the author's email using 'git show' command
                def authorEmail = sh(returnStdout
