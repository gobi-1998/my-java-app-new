pipeline {
    agent any

    parameters {
        string(name: 'webhookToken', defaultValue: '', description: 'GitHub webhook token')
    }

    pipeline {
    agent any
    parameters {
        string(name: 'webhookToken', defaultValue: '', description: 'GitHub webhook token')
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    def receivedToken = currentBuild.rawBuild.getCause(hudson.model.Cause$UpstreamCause).getUpstreamBuild().getAction(hudson.model.ParametersAction).getParameter('webhookToken').getValue()
                    def configuredToken = params.webhookToken

                    if (receivedToken == configuredToken) {
                        echo 'Webhook token is valid. Starting the build.'
                        // Add your build steps here
                    } else {
                        error('Webhook token is invalid. Access denied.')
                    }
                }
            }
        }
    }
}


        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
    }
}
