pipeline {
    agent any
    environment {
        PROJECT_ID = 'boxwood-destiny-401815'
        CLUSTER_NAME = 'autopilot-cluster'
        LOCATION = 'australia-southeast2'
        CREDENTIALS_ID = '89d535d3-12ce-47ba-9d62-cd51c2ea692f'
    }
    
    stages{
        stage('Git checkout'){
            steps{
                checkout scm
            }
            
        }
    
    // stage('Push Dockerfile to Ansible'){
    //     sshagent(['ansible']) {
    //     sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70'
    //     sh 'scp -r /var/lib/jenkins/workspace/pipeline-ci/* tantely@34.129.65.70:/home/tantely'
    //     }
    // }
    
        stage('Docker build image'){
            steps{
                script{
                    website = docker.build("ranjarat/website:0.${env.BUILD_ID}")
                }
            }
        }
    
    // stage('Docker tag image'){
    //     sshagent(['ansible']){
    //         sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70 cd /home/tantely'
    //         sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70 docker image tag website:0.$BUILD_ID ranjarat/website:0.$BUILD_ID'
    //         sh 'ssh -o StrictHostKeyChecking=no tantely@34.129.65.70 docker image tag website:0.$BUILD_ID ranjarat/website:latest'
    //     }
    // }
    
        stage('Push image'){
            steps{
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'hub') {
                        website.push("latest")
                        website.push("0.${env.BUILD_ID}")
                    }
                }
            }
        }
    
        stage('Deploy to GKE'){
            steps{
                sh "sed -i 's/website:latest/website:0.${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'Deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
}
}
