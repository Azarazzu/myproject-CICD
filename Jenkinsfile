pipeline {
    agent any
    environment {
        PROJECT_ID = 'isolvepeppol-359711'
        CLUSTER_NAME = 'demo-jenkins'
        LOCATION = 'us-central1-c'
        CREDENTIALS_ID = 'gke'
        registryCredential = 'Docker-ID'
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
                    myapp = docker.build("azzu9394/hello:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', registryCredential ) {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true ])
            //     withKubeConfig([credentialsId: 'kube-config']) {
            //   sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"'
            //   sh 'chmod u+x ./kubectl'
            //   sh './kubectl apply -f deployment.yml'
            //     }
            }
        }
    }
}
