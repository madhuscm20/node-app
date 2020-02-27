pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    tools{
        maven 'maven'
    }
    stages{
       stage('code cloning'){
         steps{
            git credentialsId: 'Git_Credentials', url: 'https://github.com/padhugitpractice/JenkinsDocker.git'
               }
           }
         stage('code build by maven'){
               steps{
               sh'mvn clean package'
           }
          } 
        
        stage('Build Docker Image'){ 
            steps{
                sh "docker build . -t madhu309/madhu:${DOCKER_TAG}"
            }
        }
        stage('DockerHub Push'){
            steps{
               withCredentials([string(credentialsId: 'docker', variable: 'docker')]) {
                    sh "docker login -u madhu309 -p ${docker}"
                    sh "docker push madhu309/madhu:${DOCKER_TAG}"
               }
             }
            }
        stage('Deploy to k8s'){
            steps{
                sh "chmod 777 changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
    sshagent(['kubernetes']) {
    sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ubuntu@13.126.206.145:/home/ubuntu/"
                    script{
                        try{
                            sh "ssh ubuntu@13.126.206.145 kubectl apply -f ."
                        }catch(error){
                            sh "ssh ubuntu@13.126.206.145 kubectl create -f ."
                        }
                    }
                }
            }
      }
}

    def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
     }
}
