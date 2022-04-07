pipeline{
      agent any
    environment {
        registry = "paulcarroll/pyjenkins"
        registryCredential = 'docker-hub-login'
        dockerImage = ''

    }
    
  
    
    stages{

         
        stage("build checkout"){
            steps{
                git 'https://github.com/pauliecarroll/SimpleFlaskUI'
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
    }
}
