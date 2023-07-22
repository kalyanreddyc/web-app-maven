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
                    def aws-region='us-east-1'
                    def aws-account=969921119504
                    sh '''
                        echo $aws-region $aws-account
                        aws ecr get-login-password --region $aws-region | docker login --username AWS --password-stdin $aws-account.dkr.ecr.$aws-region.amazonaws.com
                        docker build -t javaapp:1.0 .
                        docker tag javaapp:1.0 $aws-account.dkr.ecr.$aws-region.amazonaws.com/javaapp:1.0
                        docker push $aws-account.dkr.ecr.$aws-region.amazonaws.com/javaapp:1.0
                    '''
                        
                    }
                }
        }
       // stage('Push Docker Image to ECR') {
         //   steps {
         //       script {
           //         def awsRegion = 'us-east-1'
             //       def ecrRepository = 'javaapp'
               //     def dockerImageTag = '1.0'
                 //   def awsAccountId = '969921119504'
                   // def ecrCredentials = amazonECR(credentialsId: 'kalyancisco', region: awsRegion)
                  //  def dockerImage = docker.build("${awsAccountId}.dkr.ecr.${awsRegion}.amazonaws.com/${ecrRepository}:${dockerImageTag}")
                    // withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'kalyancisco', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                    //     docker.withRegistry("https://${awsAccountId}.dkr.ecr.${awsRegion}.amazonaws.com", 'ecr') {
                    //        dockerImage.push()
                      //  }
                    //}
                //}
            //}
        //}
    }
}

