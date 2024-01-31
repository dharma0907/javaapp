pipeline{
    agent any
        environment{
            VERSION = "${env.BUILD_ID}"
        
        }
    stages{
        stage("pulling jenkins file from scm"){
            steps{
                script{
                   echo "========jenkins file from scm========"
                }
               
            }
        }
        stage("docker build and docker push"){
            steps{
             script{
                withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_pass')]) {
    
                   sh '''
                      docker build -t 34.30.190.18:8083/springapp:${VERSION} .
                      echo "pushing image"
                      docker login -u admin -p $docker_pass 34.30.190.18:8083
                      docker push 34.30.190.18:8083/springapp:${VERSION}
                      docker rmi 34.30.190.18:8083/springapp:${VERSION}
                    '''
                }
             }
           }
        }
        stage("identify any miss configurations in .yaml file using datree"){
            steps{
                script{
                    dir('kubernetes/') {
                           sh 'helm datree test myapp/'
                     }
                }
            }
        }



    }
   
}