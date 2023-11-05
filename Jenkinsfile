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
                    } else {
                        error 'Build failed.'
                    }
                }
            }
        }

        stage('Deploy to Ansible Server') {
            steps {
                script {
                    def sshServer = 'ansible'  // Replace with your SSH server name from Jenkins
                    def sourceFiles = 'target/*.jar'  // Source files to copy, change this to match your artifacts location
                    def remoteDirectory = '/opt'  // Destination directory on your Ansible server

                    sshPublisher(
                        publishers: [
                            sshPublisherConfig(
                                configName: sshServer,
                                transfers: [
                                    sshTransfer(
                                        execCommand: "scp -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o CheckHostIP=no -o PreferredAuthentications=publickey -o PasswordAuthentication=no",
                                        execTimeout: 120000, // Adjust as needed
                                        sourceFiles: sourceFiles,
                                        remoteDirectory: remoteDirectory,
                                    )
                                ]
                            )
                        ]
                    )
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
