pipeline {
    agent any

    stages {

        stage('Build') {
            agent any
            steps {
                echo "Building application..."
            }
        }

        stage('Deploy to Dev') {
            steps {
                sshagent(['new-key']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no index.html ec2-user@43.205.92.139:/usr/share/nginx/html/
                    '''
                }
                echo "Deployed to Dev server"
            }
        }

        stage('Deploy to Staging') {
        
            steps {
                sshagent(['new-key']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no index.html ec2-user@52.66.216.253:/usr/share/nginx/html/
                    '''
                }
                echo "Deployed to Staging server"
            }
        }

        stage('Manual Approval') {
            
            steps {
                input message: "Approve production deployment?"
            }
        }

      stage('Deploy to Production') {
    steps {
        sshagent(['new-key']) {
            // Test connection first
            sh 'ssh -o StrictHostKeyChecking=no ec2-user@13.204.91.120 "echo Connected"'

            // Copy file to target
            sh 'scp -o StrictHostKeyChecking=no index.html ec2-user@13.204.91.120:/usr/share/nginx/html/'
        }
        echo "Deployed to Production server"
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
}
