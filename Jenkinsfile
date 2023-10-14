pipeline {
    
    agent any
    
    stages {
        
        stage("git login"){
            steps{
                git 'https://github.com/vjdbj/CICD.git'
            }
        }
        stage("docker build"){
             steps{
        sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
        sh 'docker image tag $JOB_NAME:v1.$BUILD_ID vishalramakant/$JOB_NAME:v1.$BUILD_ID'
        sh 'docker image tag $JOB_NAME:v1.$BUILD_ID vishalramakant/$JOB_NAME:latest'
             }
         }
        stage("pushing images to dockerhub"){
             steps{
                 withCredentials([string(credentialsId: 'dockerhub-password', variable: 'passwd')]) {
                sh "docker login -u vishalramakant -p ${passwd}"
                sh 'docker image push vishalramakant/$JOB_NAME:v1.$BUILD_ID'    
                sh 'docker image push vishalramakant/$JOB_NAME:latest'
                
                sh 'docker image rm $JOB_NAME:v1.$BUILD_ID vishalramakant/$JOB_NAME:v1.$BUILD_ID vishalramakant/$JOB_NAME:latest'
                 }
        
             }
        }
        stage("docker container deployment"){
            steps{
                sshagent(['webapp']) {
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.17.43 docker rm -f jj-container'
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.17.43 docker rmi -f vishalramakant/demo-1'
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.17.43 docker run -itd --name jj-container -p 9000:80 vishalramakant/demo-1'
                }
            }
        }
    }
}
