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
                        echo 'Build failed.'
                        // You can add error handling or notifications here
                    }
                }
            }
        }

        // Add more stages for deployment or other actions as needed
    }

    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed.'
            // You can add error handling or notifications here
        }
    }
}
