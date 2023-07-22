pipeline {
    agent any
    stages {
        stage('Build and Test') {
            steps {
                //build and create aa package
                sh 'mvn clean package'
            }
        }
        stage('Code Coverage with Sonarqube') {
             steps {
                 // sonar 
                 sh 'mvn sonar:sonar'
                 }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    //def dockerImage = docker.build('javaapp:1.0', '.')
                    sh '''
                            awsRegion=\'us-east-1\'
                            ecrRepository=\'javaapp\'
                            dockerImageTag=\'1.0\'
                            awsAccountId=\'969921119504\'
                            credentialsId=\'kalyancisco\'
                            
                            # Set AWS credentials for AWS CLI (replace the values with your actual AWS credentials)
                            export AWS_ACCESS_KEY_ID="YOUR_AWS_ACCESS_KEY_ID"
                            export AWS_SECRET_ACCESS_KEY="YOUR_AWS_SECRET_ACCESS_KEY"
                            
                            # Authenticate Docker with AWS ECR (assuming AWS CLI is already installed and configured)
                            aws ecr get-login-password --region $awsRegion | docker login --username AWS --password-stdin ${awsAccountId}.dkr.ecr.${awsRegion}.amazonaws.com
                            
                            # Build the Docker image
                            docker build -t "${awsAccountId}.dkr.ecr.${awsRegion}.amazonaws.com/${ecrRepository}:${dockerImageTag}" .
                            
                            # Push the Docker image to AWS ECR
                            docker push "${awsAccountId}.dkr.ecr.${awsRegion}.amazonaws.com/${ecrRepository}:${dockerImageTag}"
                    '''
                        
                    }
                }
        }
        stage('Push Docker Image to ECR') {
            steps {
                script {
                    def awsRegion = 'us-east-1'
                    def ecrRepository = 'javaapp'
                    def dockerImageTag = '1.0'
                    def awsAccountId = '969921119504'
                    def ecrCredentials = amazonECR(credentialsId: 'kalyancisco', region: awsRegion)
                    def dockerImage = docker.build("${awsAccountId}.dkr.ecr.${awsRegion}.amazonaws.com/${ecrRepository}:${dockerImageTag}")
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'kalyancisco', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                         docker.withRegistry("https://${awsAccountId}.dkr.ecr.${awsRegion}.amazonaws.com", 'ecr') {
                            dockerImage.push()
                        }
                    }
                }
            }
        }
    }
}

