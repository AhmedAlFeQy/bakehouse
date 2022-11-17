
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
                  echo ${BUILD_NUMBER} > ../bakehouse-build-number.txt
              """
          }
        } 
      }
    }
    stage('deploy') {
    
      steps {
        script {

            withCredentials([file(credentialsId: 'key', variable: 'key')]) {
              sh """
                  gcloud auth activate-service-account manage-sa@feki-368302.iam.gserviceaccount.com --key-file ${key}
        
                """
            }
        


            withCredentials([file(credentialsId: 'kube', variable: 'KUBECONFIG')]) {
              sh """
                  export BUILD_NUMBER=\$(cat ../bakehouse-build-number.txt)
                  mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                  cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                  rm -f Deployment/deploy.yaml.tmp
                  kubectl apply -f Deployment --kubeconfig=${KUBECONFIG}
                """
            }
        }
      }
    }
  }
}
