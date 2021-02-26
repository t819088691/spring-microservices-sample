
pipeline {
  agent {
    node {
      label 'maven'
    }
  }

    environment {
        DOCKER_CREDENTIAL_ID = 'dockerhub-id'
        KUBECONFIG_CREDENTIAL_ID = 'kubeconfig'
    }

    stages {
        stage ('build') {
            steps {
                container ('maven') {
                    sh 'mvn clean package'
                    sh 'cd config-service &&  docker build -t t819088691/demo/config-sample:latest .'
                    sh 'cd department-service &&  docker build -t t819088691/demo/department-sample:latest .'
                    sh 'cd discovery-service &&  docker build -t t819088691/demo/eureka-sample:latest .'
                    sh 'cd employee-service &&  docker build -t t819088691/demo/employee-sample:latest .'
                    sh 'cd gateway-service &&  docker build -t t819088691/demo/gateway-sample:latest .'
                    sh 'cd organization-service &&  docker build -t t819088691/demo/organization-sample:latest .'
                    sh 'cd proxy-service &&  docker build -t t819088691/demo/proxy-sample:latest .'
                }
            }
        }

        stage('push latest'){
           when{
             branch 'master'
           }
           steps{
                container ('maven') {
                    withCredentials([usernamePassword(passwordVariable : 'DOCKER_PASSWORD' ,usernameVariable : 'DOCKER_USERNAME' ,credentialsId : "$DOCKER_CREDENTIAL_ID" ,)]) {
                        sh 'echo "$DOCKER_PASSWORD" | docker login $REGISTRY -u "$DOCKER_USERNAME" --password-stdin'
                        sh '''
                        docker push t819088691/demo/config-sample:latest
                        docker push t819088691/demo/department-sample:latest
                        docker push t819088691/demo/eureka-sample:latest
                        docker push t819088691/demo/employee-sample:latest
                        docker push t819088691/demo/organization-sample:latest
                        docker push t819088691/demo/proxy-sample:latest
                        docker push t819088691/demo/gateway-sample:latest
                        '''
                    }
                }
           }
        }

        stage('deploy to kubernetes') {
          when{
            branch 'master'
          }
          steps {
            kubernetesDeploy(configs: '*/kubernetes.yaml', enableConfigSubstitution: true, kubeconfigId: "$KUBECONFIG_CREDENTIAL_ID")
          }
        }
    }
}