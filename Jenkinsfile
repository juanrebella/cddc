pipeline {
    agent any
    environment {
        PROJECT_ID = 'phonic-skyline-364017'
        CLUSTER_NAME = 'cluster-two'
        LOCATION = 'us-central1-c'
        CREDENTIALS_ID = 'my-project'
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("nachorebella/hello:${env.BUILD_ID}")
                }
            }
        }
	
	stage('Push image') {
            steps {
                script {
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', \
                                credentialsId: 'dockerhub', \
                                usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {

                                sh "docker login -u $USERNAME -p $PASSWORD"
                         }
                       myapp.push("${env.BUILD_ID}")
                       myapp.push('latest')
                        }
                    }
                 }


        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }    
}

