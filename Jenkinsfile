pipeline{
    agent any 
    environment{
        VERSION = "${env.BUILD_ID}"
    }
        stages{
          stage("docker build & docker push"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'nexus_creds', variable: 'nexus_password')]) {
                             sh '''
                                docker build -t 3.85.123.58:8083/springapp:${VERSION} .
                                docker login -u admin -p $nexus_password 3.85.123.58:8083 
                                docker push  3.85.123.58:8083/springapp:${VERSION}
                                docker rmi 3.85.123.58:8083/springapp:${VERSION}
                                helm version
                            '''
                    }
                }
            }
        }
        stage('Identify misconfig using datree in the helm charts'){

            steps{

                script{

                    dir('kubernetes/myapp/') {
                      withEnv(['DATREE_TOKEN=0efa386a-825b-45a1-ab80-46a74870a6c3']) {
                        sh 'helm datree test .'
                    }
                 } 
             }
         }
        }
        stage('pushing the helm chart to Nexus repo'){

            steps{

                script{
                    withCredentials([string(credentialsId: 'nexus_creds', variable: 'nexus_password')]) {
                        dir('kubernetes/') {
                          sh '''
                           helmversion=$(helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' ') 
                           tar -czvf myapp-${helmversion}.tgz myapp/
                           curl -u admin:$nexus_creds http://3.85.123.58:8081/repository/helm-repo/ --upload-file myapp-${helmversion}.tgz -v
                           '''
                        }
                    }
                }
            }
        }
    }     
           
}

