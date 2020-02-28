pipeline {
   agent any

   environment {
     // You must set the following environment variables
     SERVICE_NAME = "fleetman-api-gateway"
     REPOSITORY_TAG ="${YOUR_DOCKERHUB_USERNAME}/${SERVICE_NAME}:${BUILD_ID}"

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

//      stage('Build Image') {
//        steps {
//           sh 'docker image build -t ${REPOSITORY_TAG} .'
//         }
//      }

      stage('Build Image') {
        steps{
          script {
            dockerImage = docker.build ":$REPOSITORY_TAG"
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
