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
    
        stage('build image'){
            steps{
                script{
                    website = docker.build("ranjarat/website:0.${env.BUILD_ID}")
                }
            }
        }
    
        stage('Push image'){
            steps{
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'hub') {
                        website.push("latest")
                        website.push("0.${env.BUILD_ID}")
                    }
                    sh 'docker rmi 0.${env.BUILD_ID}'
                }
            }
        }
    
        stage('Deploy to GKE'){
            steps{
                sh "sed -i 's/website:latest/website:0.${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
}
}
