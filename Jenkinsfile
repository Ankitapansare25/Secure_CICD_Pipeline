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
                sh 'scp -o StrictHostKeyChecking=no index.html ec2-user@15.207.237.182:/usr/share/nginx/html/'
                echo "Deployed to Dev server"
            }
        }

        stage('Deploy to Staging') {
            agent any
            steps {
                sh 'scp -o StrictHostKeyChecking=no index.html ec2-user@13.202.79.75:/usr/share/nginx/html/'
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
                sh 'scp -o StrictHostKeyChecking=no index.html ec2-user@43.205.108.206:/usr/share/nginx/html/'
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