de82843068274c8089289a25d464b7b2

pipeline {
    agent any
    environment {
        S3_BUCKET = 'webapp-newbucket'
        APP_NAME = 'webapp'
        DEPLOYMENT_GROUP = 'webappdeployment'
        AWS_REGION = 'us-east-1'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.dev/bhimarajuneelagiri/Web-app'
            }
        }
        stage('Build') {
            steps {
                sh 'echo "Building Application..."'
            }
        }
        stage('Package and Upload to S3') {
            steps {
                sh 'zip -r artifact.zip *'
                withAWS(region: AWS_REGION, credentials: 'aws-credentials') {
                    s3Upload(file: 'artifact.zip', bucket: S3_BUCKET, path: 'artifacts/artifact.zip')
                }
            }
        }
        stage('Deploy to EC2') {
            steps {
                codedeploy(
                    application: APP_NAME,
                    deploymentGroup: DEPLOYMENT_GROUP,
                    region: AWS_REGION,
                    s3Bucket: S3_BUCKET,
                    s3Key: 'artifacts/artifact.zip'
                )
            }
        }
    }
}
