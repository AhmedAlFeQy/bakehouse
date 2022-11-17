
pipeline {
  agent { label "slave"}
  stages {
    stage('build') {
      steps {
        script {
       
            withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'username', passwordVariable: 'password')]) {
              sh """
        
                  docker login -u ${username} -p ${password}
                  docker build -t ahmedalfeqy/vfbakehouse:${BUILD_NUMBER} .
                  docker push ahmedalfeqy/vfbakehouse:${BUILD_NUMBER}
              """
          }
        } 
      }
    }
    stage('deploy') {
    
      steps {
        script {

            withCredentials([file(credentialsId: 'sa', variable: 'key')]) {
              sh """
                  gcloud auth activate-service-account ${} --key-file ${key}
        
                """
            }
        }


            withCredentials([file(credentialsId: 'kube', variable: 'KUBECONFIG')]) {
              sh """
                  
                  kubectl apply -f Deployment --kubeconfig=${KUBECONFIG}
                """
            }
        }
      }
    }
  }
}