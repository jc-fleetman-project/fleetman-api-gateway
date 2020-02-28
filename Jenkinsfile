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

     // GCE
     PROJECT_ID = 'capable-arbor-268514'
     CLUSTER_NAME = 'cluster-1'
     LOCATION = 'us-central1-a'
     MANIFEST = 'deploy.yaml'
     CREDENTIALS_ID = 'gke'
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
      }

      stage('Deploy to Cluster') {
          steps {
            // ersetzt die Umgebungsvariable REPOSITORY_TAG im Kubernetes Deployment
            sh "sed -i -e 's/${REPOSITORY_TAG}/'+${REPOSITORY_TAG}+'/g' hello.txt"

            // Führt das Deployment aus
            step([
              $class: 'KubernetesEngineBuilder',
              projectId: env.PROJECT_ID,
              clusterName: env.CLUSTER_NAME,
              location: env.LOCATION,
              manifestPattern: env.MANIFEST,
              credentialsId: env.CREDENTIALS_ID,
              verifyDeployments: true])
          }
      }

      stage('Remove Unused docker image') {
        steps{
          sh "docker rmi $REPOSITORY_TAG"
        }
      }
   }
}
