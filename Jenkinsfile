pipeline {
   agent any

   environment {
     // You must set the following environment variables
     // ORGANIZATION_NAME
     // YOUR_DOCKERHUB_USERNAME (it doesn't matter if you don't have one)
     registryCredential = 'f6e9ff9f-af1a-4db6-b139-d4219cbf0d4e' 
     SERVICE_NAME = "fleetman-webapp"
     REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
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
            bat "echo No build required for Webapp $REPOSITORY_TAG."
         }
      }

      stage('Build and Push Image') {
         steps {
            bat "docker image build -t  $REPOSITORY_TAG ."
           // bat "docker push $REPOSITORY_TAG"
            docker.withRegistry( REPOSITORY_TAG, registryCredential ) { 

                        dockerImage.push() 

                    }
         }
      }

      stage('Deploy to Cluster') {
          steps {
             withKubeConfig([credentialsId: 'Jenkins_serviceAccount', serverUrl: 'http://127.0.0.1:57086/']) {
      sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
       }
           //bat "kubectl apply -f deploy.yaml "
           //  bat "kubectl get pods"
          }
      }
   }
}
