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
                sh '''
              docker build . -t javaapp:$BUILD_NUMBER
              docker tag javaapp:$BUILD_NUMBER 620535894742.dkr.ecr.us-east-1.amazonaws.com/javaapp:$BUILD_NUMBER
                
                '''
                
            }
        }
        stage('Push Docker Image') {
            steps{
                 withAWS(credentials: 'kalyancisco', region: 'us-east-1') {
                    sh '''
                    aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 620535894742.dkr.ecr.us-east-1.amazonaws.com
                    docker push 620535894742.dkr.ecr.us-east-1.amazonaws.com/javaapp:$BUILD_NUMBER
                     '''
                }
            } 

        }
   

    }
}

