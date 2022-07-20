pipeline {
  agent any
  environment {
    DOCKERHUB_CREDENTIALS_PSW="f1d00111-ecbc-48ed-b5cf-8d99e2a9e5d4"
    DOCKERHUB_CREDENTIALS_USR="rishabhbhojak" 
        
  }      
  stages {
    stage("Verify required tooling") {
      steps {
        sh '''
          docker version
          docker info
          docker-compose --version 
           
        '''
      }
    }
    stage('Prune Docker data') {
      steps {
        sh 'docker system prune -a --volumes -f'
      }
    }
    
    stage('Build') {
      steps {
        sh ' docker build -t rishabhbhojak/mynodeimagefresh .' 
      }
    }  

    stage('Login to dockerhub') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }

    stage('Push images to dockerhub') {
      steps {
        sh 'docker push rishabhbhojak/mynodeimagefresh'
      }
    }

    stage('Deploy Mongodb') {
      steps {
       
        sh '''
          oc login --token=sha256~eEtl5W78AJIcFPwiwz3LuSWYmSC0fMPVTINk1cBFvuA --server=https://c115-e.us-south.containers.cloud.ibm.com:32528
          oc new-project nodeapplication
          oc project nodeapplication
          oc apply -f mongodb-secret.yaml
          oc apply -f mongo-configmap.yaml
          oc apply -f mongodb-deployment.yaml
        '''  
        
       
    }
    
    }
    
    stage('Sleep') {
      steps {
        sh 'sleep 150'
       
      }
    
    }

    stage('Deploy Application') {
      steps {
        sh 'oc apply -f loadapp-deployment.yaml'
        sh 'oc apply -f routedeployment.yaml'
       
      }
    
    }

  }
  
}
