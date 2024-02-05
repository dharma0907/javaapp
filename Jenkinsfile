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
                      docker build -t 35.224.165.189:8083/springapp:${VERSION} .
                      echo "pushing image"
                      docker login -u admin -p $docker_pass 35.224.165.189:8083
                      docker push 35.224.165.189:8083/springapp:${VERSION}
                      docker rmi 35.224.165.189:8083/springapp:${VERSION}
                    '''
                }
             }
           }
        }
        /*stage("identify any miss configurations in .yaml file using datree"){
            steps{
                script{
                    dir('kubernetes/') {
                           sh 'helm datree test kubernetes/myapp/'
                     }
                }
                withKubeConfig(caCertificate: '', clusterName: 'ubuntu', contextName: '', credentialsId: 'kconfig', namespace: '', restrictKubeConfigAccess: false, serverUrl: 'https://10.128.0.3:6443') {
                        dir('kubernetes/') {
                          sh 'helm upgrade --install --set image.repository=":8083/springapp" --set image.tag="${VERSION}" myjavaapp myapp/ ' 
                        }
                    }
            }
        }*/
        stage("pusing hel charts to nexus"){
            steps{
             script{
                withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_pass')]) {
                   dir('kubernetes/'){
                   sh '''

                       helmversion=$(helm show chart myapp | grep version |cut -d: -f 2 |  tr -d ' ')
                       tar -czvf  myapp-${helmversion}.tgz myapp/
                       curl -u admin:$docker_pass http://35.224.165.189:8081/repository/helm-hosted/ --upload-file myapp-${helmversion}.tgz -v
                    
                    '''
                   }
                }

             }
           }
        }
        stage('Deploying application on k8s cluster') {
            steps {
               script{
                  //withKubeCredentials(kubectlCredentials: [[caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJSW1aZE9FTEo1R2d3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TkRBeE16QXdOelUzTXpOYUZ3MHpOREF4TWpjd09EQXlNek5hTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUURJalB0YXgvakhmenBMQXRhWS9Qem1ZVmtjRzdEUmI5Y3V1cThOcTVURGI1Zy83cG0wdnl1NzR3NWwKNzQvczJpYXhzUlBlb3NreDRIc0dQWEJ6RjQrZ0pKMlhqSlZTSkpxUklRRWhrY1RKak9HZ2RvbTVEZW5wWFQ2cgpwV0xwVklHM3JZL3oxUXZTbS9nWU5IY29GU2FCRTNiTGtRTytGZUk3Vk5qUjhiTFp3MVpSV0xyRHBEK2U2VWZkCndmU2dIVTl3NzFvR3ovUU5PWkJkdElPbE1JbGJIZ0JsUGtjUHhjYTYzSUFxcm5RUG5NeFFhVWlPRWhOdXg0VSsKa1hJcHpLcGJvays1clFIRTE3TFJ5a0tSb3RqdEQ2K29HN3BHU0IrV2lXaE1KVjRXU28wanpZcnNDcnd3NnBobQo3L3NDSWF6emI5bU1vWlRVN3BJajc3NTBtTWZUQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJTTzFIejI3NVFYRnJESkZOcVZETFZ3Q0gxZHNEQVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQyt1cmNMWGltRApIYndOaWhIZFpwR1ZKRXNBeGMvRys1UEZGSzc2MXhHZUR0NTJ5RzEvSGt4ZGxWSitSTkVDdkN1SEdRRnJWcWpDCktjWGFWeTUzSlpORGpNNzB4TjlKamhBUDNraDZuTnNZTk5xZ0VZbWRwbEpDWVF6V3U0ZnBYWExLNithWUNQZjAKMmZDM0tjU3h0SWVEd1o1a25YWnNkRUJVNWY4N2l1bzFPU2Izc3dnMlZkTDMrdDliYVhkRlhPM0w4ZVBpWm4vbQoreEJ2ZUNsSEd6SGduTi9nWDRCNmpBcVhGajk5cmxZVlNEeXA0VTZYTUIzUmpZZzhjTGo3VGVZZVorRmVjVUM5CnpXeGcyOTc3bzd1MFRwOThHNTFQbXNXSmdKSFp6aEdwREtuWDN2d1dwcCtPTWJFVkhlZ3FTajErN2VVd0JKNjUKZmd6ODRCWVUyWFNTCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K', clusterName: '', contextName: '', credentialsId: 'secret_token', namespace: 'jenkins', serverUrl: '']])  {
                   withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: '', contextName: '', credentialsId: 'secret-token', namespace: 'jenkins', serverUrl: "https://10.128.0.3:6443"]]) {
                   sh '''
                   sudo -i
                   echo 'enter into k8 cluster'
                   pwd
                   kubectl get nodes
                   ''' 
                   }
               }
            }
        }

 

        }



    }
