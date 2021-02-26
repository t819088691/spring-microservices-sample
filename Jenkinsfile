
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
                    sh 'cd config-service &&  docker build -t config-sample:latest .'
                    sh 'cd department-service &&  docker build -t department-sample:latest .'
                    sh 'cd discovery-service &&  docker build -t eureka-sample:latest .'
                    sh 'cd employee-service &&  docker build -t employee-sample:latest .'
                    sh 'cd gateway-service &&  docker build -t gateway-sample:latest .'
                    sh 'cd organization-service &&  docker build -t organization-sample:latest .'
                    sh 'cd proxy-service &&  docker build -t proxy-sample:latest .'
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
                        sh 'echo "$DOCKER_PASSWORD" | docker login 192.168.23.157 $REGISTRY -u "$DOCKER_USERNAME" --password-stdin'
                        sh '''
                        docker tag config-sample:latest       192.168.23.157/demo/config-sample:latest
                        docker tag department-sample:latest   192.168.23.157/demo/department-sample:latest
                        docker tag eureka-sample:latest       192.168.23.157/demo/eureka-sample:latest
                        docker tag employee-sample:latest     192.168.23.157/demo/employee-sample:latest
                        docker tag gateway-sample:latest      192.168.23.157/demo/gateway-sample:latest
                        docker tag organization-sample:latest 192.168.23.157/demo/organization-sample:latest
                        docker tag proxy-sample:latest        192.168.23.157/demo/proxy-sample:latest
                        docker push 192.168.23.157/demo/config-sample:latest
                        docker push 192.168.23.157/demo/department-sample:latest
                        docker push 192.168.23.157/demo/eureka-sample:latest
                        docker push 192.168.23.157/demo/employee-sample:latest
                        docker push 192.168.23.157/demo/organization-sample:latest
                        docker push 192.168.23.157/demo/proxy-sample:latest
                        docker push 192.168.23.157/demo/gateway-sample:latest
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