pipeline {
    agent any
    options {
        // Timeout counter starts AFTER agent is allocated
        timeout(time: 1, unit: 'SECONDS')
    }
    stages {
        stage('Build and Test') {
            steps {
                //build and create a package
                sh 'mvn clean package'
            }
        }
        stage('Code Coverage with Sonarqube') {
             steps {
                 // sonar 
                 sh 'mvn sonar:sonar'
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
