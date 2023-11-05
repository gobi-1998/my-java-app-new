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
                    def server = getSSHServer("Ansible Server") // Use the name you configured in Jenkins
                    sshPublisher(publishers: [sshPublisherConfig(
                        transfers: [sshTransfer(
                            execCommand: "scp -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o CheckHostIP=no -o PreferredAuthentications=publickey -o PasswordAuthentication=no",
                            execTimeout: 120000, // Adjust as needed
                            from: "target/*", // Source files to copy, change this to match your artifacts location
                            remoteDirectory: "/opt", // Destination directory on AWS instance is /opt
                        )],
                        serverName: server.name,
                    )])
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
