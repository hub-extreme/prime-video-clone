pipeline {
    agent any

    tools {
        nodejs 'node23' // Ensure 'node16' is configured in Jenkins under Manage Jenkins > Global Tool Configuration
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/hub-extreme/prime-video-clone.git'
            }
        }
    
        stage ("Trivy File Scan") {
            steps {
                sh "trivy fs . > trivy.txt"
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image tagged as 'prime-clone:latest'
                    sh "docker build -t prime-clone:latest ."
                    sh "docker tag prime-clone:latest yuvrajdocker001/prime-clone:latest"
                }
            }
        }
        
        stage('Push to DockerHub') {
            steps {
                script {
                    withDockerRegistry([ credentialsId: 'dockerhub', url: '' ]) {
                        
                        sh "docker push yuvrajdocker001/prime-clone:latest"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig(caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8s', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://678D754BCF0D0E8BA410E9B076F0BD19.gr7.ap-south-1.eks.amazonaws.com') {
                        sh """
                            
                            
                            # Navigate to the Kubernetes manifests directory
                            cd Kubernetes
                            
                            # Apply all Kubernetes manifests
                            kubectl apply -f .
                            
                        """ 
                       
                       
                    }
                }
            }
        }
    }
}
