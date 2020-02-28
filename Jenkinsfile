pipeline {
   agent any

   environment {
     // Name des Service
     SERVICE_NAME = "fleetman-api-gateway"

     // Tag für Docker, bestehend aus Repository Name und Tag des Image
     REPOSITORY_TAG ="${YOUR_DOCKERHUB_USERNAME}/${SERVICE_NAME}:${BUILD_ID}"

     // dockerhub Zugangsdaten
     DOCKERHUB_CREDENTIALS = 'dockerhub'
     dockerImage = ''
   }

   stages {
      stage('Preparation') {
         steps {
            cleanWs()
            git credentialsId: 'GitHub', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
         }
      }
      stage('Build') {
         steps {
            sh '''mvn clean package'''
         }
      }

      stage('Build Image') {
        steps{
          script {
            dockerImage = docker.build "$REPOSITORY_TAG"
          }
        }
      }

      stage('Push Image') {
        steps{
          script {
            docker.withRegistry( '', DOCKERHUB_CREDENTIALS ) {
            dockerImage.push()
          }
        }
      }

      stage('Remove Unused docker image') {
        steps{
          sh "docker rmi $REPOSITORY_TAG"
        }
      }



//      stage('Deploy to Cluster') {
//          steps {
//                    sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
//          }
//      }
   }
}
