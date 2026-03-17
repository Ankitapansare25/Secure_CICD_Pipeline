pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo "Building application..."
            }
        }

        stage('Deploy to Dev') {
            steps {
                sh 'scp -o StrictHostKeyChecking=no index.html ec2-user@13.203.23.101:/usr/share/nginx/html/'
                echo "Deployed to Dev server"
            }
        }

        stage('Deploy to Staging') {
            steps {
                sh 'scp -o StrictHostKeyChecking=no index.html ec2-user@3.108.197.30:/usr/share/nginx/html/'
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
                sh 'scp -o StrictHostKeyChecking=no index.html ec2-user@3.110.98.94:/usr/share/nginx/html/'
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