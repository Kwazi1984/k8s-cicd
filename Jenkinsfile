pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stages{
        // stage('Build Docker Image'){
        //     steps{
        //         sh "docker build . -t kwazi1984/nodeapp:${DOCKER_TAG} "
        //     }
        // }
        // stage('DockerHub Push'){
        //     steps{
        //         withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
        //             sh "docker login -u kwazi1984 -p ${dockerHubPwd}"
        //             sh "docker push kwazi1984/nodeapp:${DOCKER_TAG}"
        //         }
        //     }
        // }
        stage('Deploy to Kubernetes'){
            steps{
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                sh "sshpass -p ubutnu ssh ubuntu@10.0.2.15 'kubectl version'"
                //sh "kubectl apply -n cicd -f services.yml node-app-pod.yml"              
            }
        }
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}