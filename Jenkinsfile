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
                    scp -o StrictHostKeyChecking=no index.html ec2-user@13.200.217.134:/usr/share/nginx/html/
                    '''
                }
                echo "Deployed to Dev server"
            }
        }

        stage('Deploy to Staging') {
        
            steps {
                sshagent(['new-key']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no index.html ec2-user@13.200.9.90:/usr/share/nginx/html/
                    '''
                }
                echo "Deployed to Staging server"
            }
        }

        stage('Manual Approval') {
            
            steps {
                timeout(time: 2, unit: 'MINUTES') {
    input message: "Approve production deployment?", ok: "Deploy"
         }
        }
        }

        stage('Deploy to Production') {
            

            steps {
                sshagent(['new-key']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no index.html ec2-user@13.202.46.86:/usr/share/nginx/html/
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
}
