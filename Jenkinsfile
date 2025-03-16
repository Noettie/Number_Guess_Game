pipeline {
    agent any

    environment {
        REMOTE_USER = 'ec2-user'  // Remote server username
        REMOTE_HOST = '44.203.118.134'  // Remote server IP or hostname
        REMOTE_TOMCAT_WEBAPPS = '/opt/tomcat/webapps'  // Tomcat webapps directory on the remote server
    }

    stages {
        // Stage 1: Clone the GitHub repository
        stage('Clone Repository') {
            steps {
                echo 'Cloning the GitHub repository...'
                git url: 'https://github.com/Noettie/Number_Guess_Game.git', branch: 'main'
            }
        }

        // Stage 2: Build the project using Maven
        stage('Build') {
            steps {
                echo 'Building the project with Maven...'
                sh 'mvn clean package'
            }
        }

        // Stage 3: Run unit tests
        stage('Test') {
            steps {
                echo 'Running unit tests...'
                sh 'mvn test'
            }
        }

        // Stage 4: Deploy the application to the remote Tomcat server
        stage('Deploy') {
            steps {
                echo 'Deploying the application to the remote Tomcat server...'
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'jenkins-ssh-key',  // Replace with your actual credential ID
                    keyFileVariable: 'SSH_KEY'
                )]) {
                    sh """
                        scp -o StrictHostKeyChecking=no -i ${SSH_KEY} \
                            target/NumberGuessGame-1.0.war \
                            ${REMOTE_USER}@${REMOTE_HOST}:${REMOTE_TOMCAT_WEBAPPS}/
                    """
                }
            }
        }

        // Stage 5: Verify the deployment
        stage('Verify Deployment') {
            steps {
                echo 'Verifying the deployment...'
                // Example: Check if the application is accessible
                sh 'curl http://${REMOTE_HOST}:8080/NumberGuessGame-1.0/'
                echo 'Verification completed!'
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}
