pipeline{
      agent any
    environment {
        registry = "paulcarroll/pyjenkins"
        registryCredential = 'docker-hub-login'
        DOCKER_ID = credentials('DOCKER_ID')
        DOCKER_PASSWORD = credentials('DOCKER_PASSWORD')
    }
    
  
    
    stages{

         
        stage("build checkout"){
            steps{
                git 'https://github.com/pauliecarroll/SimpleFlaskUI'
            }
            
        }
        
        stage{
            steps{
                echo 'Initializing..'
                echo "running ${env.BUILD_ID} on ${env.JENKINS_URL}"
                echo "Current branch: ${env.BRANCH_NAME}"
                sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_ID --password-stdin'
            }
        
        stage("build image"){
            steps{
                echo 'building..'
                sh 'docker build -t $DOCKER_ID/${JOB_NAME}'

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
