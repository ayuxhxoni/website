pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-south-1:123456789'
       appRegistry = "746458806311.dkr.ecr.ap-northeast-1.amazonaws.com/ayushscapstone"
       capstoneRegistry = "https://746458806311.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "ayushcapstone"
        service = "ayushcapstone"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/ayuxhxoni/website'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: '123456789', region: 'ap-south-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}