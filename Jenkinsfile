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
                // Add your build steps here
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
                // Add your deployment steps here
            }
        }

        stage('Test') {
            steps {
                echo "Testing in ${params.ENVIRONMENT} environment"
                // Add your testing steps here
            }
        }
    }

    post {
        always {
            echo "Pipeline completed for ${params.ENVIRONMENT} environment"
        }
        success {
            script {
                def changeSet = currentBuild.rawBuild.changeSets.iterator().next()
                def authorEmail = changeSet.items[0].authorEmail
                emailext subject: "Pipeline Successful for ${params.ENVIRONMENT} environment",
                          body: "The pipeline for ${params.ENVIRONMENT} environment has completed successfully.",
                          to: authorEmail
            }
        }
        failure {
            script {
                def changeSet = currentBuild.rawBuild.changeSets.iterator().next()
                def authorEmail = changeSet.items[0].authorEmail
                emailext subject: "Pipeline Failed for ${params.ENVIRONMENT} environment",
                          body: "The pipeline for ${params.ENVIRONMENT} environment has failed. Please investigate.",
                          to: authorEmail
            }
        }
    }
}
