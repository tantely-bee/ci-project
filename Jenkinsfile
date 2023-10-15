node {
    
    environment {
        PROJECT_ID = 'boxwood-destiny-401815'
        CLUSTER_NAME = 'autopilot-cluster'
        LOCATION = 'australia-southeast2'
        CREDENTIALS_ID = 'gke-kubernetes'
    }
    
    stage('Git checkout'){
        //git 'https://github.com/tantely-bee/ci-project.git'
        checkout scm
    }
    
    stage('Push Dockerfile to Ansible'){
        sshagent(['ansible']) {
        sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70'
        sh 'scp -r /var/lib/jenkins/workspace/pipeline-ci/* tantely@34.129.65.70:/home/tantely'
        }
    }
    
    stage('Docker build image'){
        sshagent(['ansible']){
            sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70 cd /home/tantely'
            sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70 docker image build -t website:0.$BUILD_ID .'
        }
    }
    
    stage('Docker tag image'){
        sshagent(['ansible']){
            sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70 cd /home/tantely'
            sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70 docker image tag website:0.$BUILD_ID ranjarat/website:0.$BUILD_ID'
            sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70 docker image tag website:0.$BUILD_ID ranjarat/website:latest'
        }
    }
    
    stage('Push image to Dockerhub'){
        sshagent(['ansible']){
            withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
                sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70 docker login -u ranjarat -p ${dockerhub-passwd}'
                //sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70 cd /home/tantely'
                sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70 docker image push ranjarat/website:0.$BUILD_ID'
                sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70 docker image push ranjarat/website:latest'
                
            }
            
        }
    }
    
    stage('Deploy to GKE'){
        
    }
}
