pipeline {
    agent none   

    stages {

        stage('Build') {
            agent any
            steps {
                echo "Building application..."
            }
        }

        stage('Deploy to Dev') {
            agent any
            steps {
                sshagent(['ec2-user']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no index.html ec2-user@13.203.96.150:/usr/share/nginx/html/
                    '''
                }
                echo "Deployed to Dev server"
            }
        }

        stage('Deploy to Staging') {
            agent any
            steps {
                sshagent(['ec2-user']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no index.html ec2-user@65.0.67.206:/usr/share/nginx/html/
                    '''
                }
                echo "Deployed to Staging server"
            }
        }

        stage('Manual Approval') {
            agent any
            steps {
                input message: "Approve production deployment?"
            }
        }

        stage('Deploy to Production') {
            agent any
            steps {
                sshagent(['ec2-user']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no index.html ec2-user@65.0.56.175:/usr/share/nginx/html/
                    '''
                }
                echo "Deployed to Production server"
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}a