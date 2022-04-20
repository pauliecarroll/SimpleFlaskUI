pipeline{
    environment {
        registry = "paulcarroll/pyjenkins"
        registryCredential = 'docker-hub-login'
        dockerImage = ''

    }
      agent any
    
  
    
    stages{

         
        stage("build checkout"){
            steps{
                git 'https://github.com/pauliecarroll/SimpleFlaskUI'
            }
            
        }
        stage ("stop existing container"){
            steps{
                sh returnStatus: true, script: 'docker stop $(docker ps -a | grep ${JOB_NAME} | awk \'{print $1}\')'
                sh returnStatus: true, script: 'docker rm ${JOB_NAME}'
                sh returnStatus: true, script: 'docker rmi $(docker images | grep ${registry} | awk \'{print $3}\') --force'
            }
        }
        
        stage("build image"){
            steps{
                script {
                    img = registry + ":${env.BUILD_ID}"
                    println("${img}")
                    dockerImage = docker.build("${img}")
                }
            }
        }

        
        stage ("testing - running in Jenkins node, runs docker image in jenkins node"){
            steps{ 
                sh "docker run -d --name ${JOB_NAME} -p 5000:5000 ${img}" 
            }
        }
        
        stage("Push to DockerHub"){
            steps {
                script {
                    docker.withRegistry("https://registry.hub.docker.com", registryCredential){
                        dockerImage.push()
                    }
                }
            }
        }
        stage ("running in staging"){
            steps{
                script{
                    def stopcontainer = "docker stop ${JOB_NAME}"
                    def delcontName = "docker rm ${JOB_NAME}"
                    def delimages = 'docker image prune -a iiforce'
                    def drun = "docker run -d --mam,e ${JOB_NAME} -p 5000:5000 ${img}"
                    sshagent(['docker-test']) {
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no docker@176.254.99.64 ${stopcontainer}"
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no docker@176.254.99.64 ${delcontainer}"
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no docker@176.254.99.64 ${delimages}"
                        sh "ssh -o StrictHostKeyChecking=no docker@176.254.99.64 ${drun}"
                        }
                }
            }
        }
    }
}
