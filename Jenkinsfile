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
                    echo "Build result: ${currentBuildResult}"  // Debug: Log the build result
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
                    def sshServer = 'ansible'  // Your SSH server configuration name
                    echo "Deploying to SSH server: ${sshServer}"  // Debug: Log the SSH server name
                    sh "ls -l ${JENKINS_HOME}/workspace/${JOB_NAME}"  // Debug: List workspace contents
                    sshPublisher(publishers: [
                        sshPublisherConfig(
                            transfers: [
                                sshTransfer(
                                    execCommand: "scp -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o CheckHostIP=no -o PreferredAuthentications=publickey -o PasswordAuthentication=no",
                                    execTimeout: 120000, // Adjust as needed
                                    sourceFiles: 'target/*.jar', // Debug: Log the source files
                                    remoteDirectory: '/opt', // Destination directory on your Ansible server
                                )
                            ],
                            serverName: sshServer
                        )
                    ])
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
