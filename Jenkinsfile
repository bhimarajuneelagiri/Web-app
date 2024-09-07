pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'    
        S3_BUCKET = 'webapp-newbucket'      
        CODEDEPLOY_APP = 'webapp'  
        CODEDEPLOY_GROUP = 'webappdeployment'  
        AWS_CREDENTIALS_ID = 'aws-credentials-id' 
    }

    stages {
        stage('Checkout Code') {
            steps {
			
                git branch: 'main', url: 'https://github.com/bhimarajuneelagiri/Web-app'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('src') {
                   
                    sh 'npm install'
                }
            }
        }

        stage('Package Application') {
            steps {
                script {
                   withAWS(credentials: "$AWS_CREDENTIALS_ID", region: "$AWS_REGION") {
                        sh '''
                        aws s3 cp web-app.zip s3://$S3_BUCKET/
                        '''
                    }
                }
            }
        }

        stage('Deploy to AWS CodeDeploy') {
            steps {
                script {
                    withAWS(credentials: "$AWS_CREDENTIALS_ID", region: "$AWS_REGION") {
                        
                        sh """
                        aws deploy create-deployment \
                            --application-name $CODEDEPLOY_APP \
                            --deployment-group-name $CODEDEPLOY_GROUP \
                            --s3-location bucket=$S3_BUCKET,key=web-app.zip,bundleType=zip \
                            --region $AWS_REGION
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment to AWS CodeDeploy was successful!'
        }
        failure {
            echo 'Deployment failed. Please check the logs.'
        }
    }
}
