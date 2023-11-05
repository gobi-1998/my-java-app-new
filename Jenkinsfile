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
                    def sshServer = [:] // Define the SSH server configuration
                    sshServer['$class'] = 'jenkins.plugins.publish_over_ssh.BapSshPublisherPlugin'
                    sshServer['plugin'] = 'publish-over-ssh@latest'

                    def transfers = [
                        [$class: 'jenkins.plugins.publish_over_ssh.BapSshTransfer',
                         remoteDirectory: '/opt', // Destination directory on your Ansible server
                         sourceFiles: 'target/*.jar', // Source files to copy, change this to match your artifacts location
                         removePrefix: 'target/',
                         remoteDirectorySDF: false,
                         flatten: true,
                         cleanRemote: false,
                         noDefaultExcludes: false,
                         makeEmptyDirs: false,
                         patternSeparator: '[, ]+']
                    ]

                    sshServer['transfers'] = transfers

                    // Publish over SSH
                    step([$class: 'BapSshPublisher', configName: 'your-ssh-config', transfers: transfers])
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
