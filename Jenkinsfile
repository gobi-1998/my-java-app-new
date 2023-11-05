pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Verify Build') {
            steps {
                script {
                    currentBuildResult = currentBuild.result
                    if (currentBuildResult == 'SUCCESS') {
                        echo 'Build was successful.'
                        // You can add further actions here if needed
                    } else {
                        error 'Build failed.'
                        // You can add error handling or notifications here
                    }
                }
            }
        }
    }

    post {
        always {
            // This block will run regardless of the build result
            echo 'This stage runs always, you can perform cleanup tasks here.'
        }
    }
}
