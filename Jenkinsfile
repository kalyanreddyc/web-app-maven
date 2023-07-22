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
                   def awsRegion = 'us-east-1'
                    def awsAccount = '969921119504'
                    def ecrLoginCommand = "aws ecr get-login-password --region ${awsRegion}"
                    
                    // Use the 'withCredentials' block to securely expose AWS credentials
                    withCredentials([
                        string(credentialsId: 'kalyancisco', variable: 'AWS_ACCESS_KEY_ID'),
                        string(credentialsId: 'kalyancisco', variable: 'AWS_SECRET_ACCESS_KEY')
                    ]) {
                        // Retrieve the AWS Access Key ID and Secret Access Key from credentials
                        def awsAccessKeyId = env.AWS_ACCESS_KEY_ID
                        def awsSecretAccessKey = env.AWS_SECRET_ACCESS_KEY

                        // Use the retrieved credentials to execute the AWS CLI command

                        def ecrPassword = sh(returnStdout: true, script: "${ecrLoginCommand}").trim()

                        // Login to Docker using the retrieved password
                        sh "echo '${ecrPassword}' | docker login --username AWS --password-stdin ${awsAccount}.dkr.ecr.${awsRegion}.amazonaws.com"

                        // Continue with the rest of the Docker commands
                        sh '''
                            docker build -t javaapp:1.0 .
                            docker tag javaapp:1.0 ${awsAccount}.dkr.ecr.${awsRegion}.amazonaws.com/javaapp:1.0
                            docker push ${awsAccount}.dkr.ecr.${awsRegion}.amazonaws.com/javaapp:1.0
                        '''
                      }
                        
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

