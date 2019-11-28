pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stages{
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t kwazi1984/nodeapp:${DOCKER_TAG} "
            }
        }
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u kwazi1984 -p ${dockerHubPwd}"
                    sh "docker push kwazi1984/nodeapp:${DOCKER_TAG}"
                }
            }
        }
        stage('Deploy to Kubernetes'){
            steps{
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                sshagent(['kops-machine']) {
                    //sh "ssh -o StrictHostKeyChecking=no ubuntu@10.0.2.15 kubectl version"
                    sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ubuntu@10.0.2.15:/home/ubuntu/jenkinks-k8s-ssh/"
                    script{
                        try{
                            sh "ssh ubuntu@10.0.2.15 kubectl apply -f /home/ubuntu/jenkinks-k8s-ssh/."
                        }catch(error){
                            sh "ssh ubuntu@10.0.2.15 kubectl create -f /home/ubuntu/jenkinks-k8s-ssh/."
                        }
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