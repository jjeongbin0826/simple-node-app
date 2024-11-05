pipeline {
    agent any
    environment {
        PROJECT_ID = 'opensource-435703'
        CLUSTER_NAME = 'kube'
        LOCATION = 'asia-northeast2-c'
        CREDENTIALS_ID = 'c885f2d1-0e96-4e54-9f9e-ca69df5019be'
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
			myapp = docker.build("jjeongbin0826/hello:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
			docker.withRegistry('https://registry.hub.docker.com', 'jjeongbin0826') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
        stage('Deploy to GKE') {
			when {
				branch 'main'
			}
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }    
}
