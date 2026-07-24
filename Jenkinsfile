pipeline {
    agent any

    environment {
        REMOTE_HOST = "ubuntu@YOUR_EC2_PUBLIC_IP"
        SSH_CREDENTIAL = "ec2-ssh-key"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Verify SSH Connection') {
            steps {
                sshagent(credentials: ["${SSH_CREDENTIAL}"]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_HOST} "
                            echo 'SSH Connection Successful'
                        "
                    """
                }
            }
        }

        stage('Update Package Repository') {
            steps {
                sshagent(credentials: ["${SSH_CREDENTIAL}"]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_HOST} "
                            sudo apt update
                        "
                    """
                }
            }
        }

        stage('Install Apache2') {
            steps {
                sshagent(credentials: ["${SSH_CREDENTIAL}"]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_HOST} "
                            sudo apt install -y apache2
                        "
                    """
                }
            }
        }

        stage('Start Apache Service') {
            steps {
                sshagent(credentials: ["${SSH_CREDENTIAL}"]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_HOST} "
                            sudo systemctl enable apache2
                            sudo systemctl start apache2
                        "
                    """
                }
            }
        }

        stage('Verify Apache Installation') {
            steps {
                sshagent(credentials: ["${SSH_CREDENTIAL}"]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_HOST} "
                            systemctl is-active apache2
                            apache2 -v
                        "
                    """
                }
            }
        }
    }

    post {

        always {
            echo 'Pipeline execution completed.'
        }

        success {
            echo 'Apache2 installed and started successfully.'
        }

        failure {
            echo 'Pipeline failed! Check console output for details.'
        }

        unstable {
            echo 'Pipeline completed but is unstable.'
        }

        aborted {
            echo 'Pipeline was aborted by the user.'
        }

        cleanup {
            cleanWs()
        }
    }
}
