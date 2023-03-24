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
                            '''
                    }
                }
            }
        }
    }    
}