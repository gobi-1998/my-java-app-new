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
                    if (currentBuild.resultIsBetterOrEqualTo('SUCCESS')) {
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
                    // Define your SSH server configuration
                    def remoteServer = [:]
                    remoteServer.name = 'ansible'
                    remoteServer.host = '172.31.40.13' // Replace with your Ansible server's IP address
                    remoteServer.port = 22
                    remoteServer.user = 'root'
                    remoteServer.password = 'admin' // Password for authentication
                    remoteServer.sourceFiles = "target/*" // Source files to copy
                    remoteServer.remoteDirectory = '/opt' // Destination directory on the remote server

                    sshPublisher(
                        configName: remoteServer.name,
                        transfers: [
                            sshTransfer(
                                execCommand: 'scp -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o CheckHostIP=no -o PreferredAuthentications=publickey -o PasswordAuthentication=no',
                                execTimeout: 120000, // Adjust as needed
                                from: remoteServer.sourceFiles,
                                remoteDirectory: remoteServer.remoteDirectory,
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
