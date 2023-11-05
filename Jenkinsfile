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
                    currentBuildResult = currentBuild.currentResult
                    echo "Build result: ${currentBuildResult}"
                    if (currentBuildResult == 'SUCCESS') {
                        echo 'Build was successful.'
                    } else {
                        error 'Build failed.'
                    }
                }
            }
        }

        stage('Deploy to Ansible Server') {
            steps {
                script {
                    def sshServer = 'ansible'
                    def transfers = [
                        [$class: 'BapSshTransfer', sourceFiles: 'target/*.jar', 
                         removePrefix: 'target/', remoteDirectory: '/opt',
                         flatten: true, cleanRemote: true, makeEmptyDirs: false, 
                         patternSeparator: '[, ]+']
                    ]
                    step([$class: 'BapSshPublisher', configName: ansible, transfers: transfers])
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
