pipeline {
    agent any

    stages {
        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build Maven') {
            steps {
                sh 'pwd'
                sh 'mvn clean install package'
            }
        }

        stage('Copy Artifacts') {
            steps {
                sh 'pwd'
                sh 'cp -r target/*.jar docker/'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def customImage = docker.build("iamsakib/petclinic:${env.BUILD_NUMBER}", "./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        customImage.push()
                    }
                }
            }
        }

        stage('Build on Kubernetes') {
            steps {
                // Wraps commands using your Jenkins Kubernetes Credentials ID
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'pwd'
                    // Fixed: Added space in 'cp -R'
                    sh 'cp -R helm/* .'
                    
                    // Fixed: Cleaned up invalid shell syntax and properly quoted the Helm command
                    // Also fixed Groovy variable interpolation syntax from $(BUILD_NUMBER) to ${env.BUILD_NUMBER}
                    sh "/usr/local/bin/helm upgrade --install petclinic-app petclinic --set image.repository=iamsakib/petclinic --set image.tag=${env.BUILD_NUMBER}"
                }
            }
        }
    }
}
