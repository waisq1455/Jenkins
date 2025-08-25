pipeline {
    agent any

    environment {
        IMAGE_NAME = "aniq47/petclinic"
        TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Copy Artifacts') {
            steps {
                script {
                    sh 'mkdir -p docker'
                    sh 'cp target/*.jar docker/ || echo "No JAR files to copy"'
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    def customImage = docker.build("${IMAGE_NAME}:${TAG}", "./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        customImage.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'kubeconfig']) {
                        sh 'cp -R helm/* .'
                        sh 'helm upgrade --install petclinic-app petclinic --set image.repository=${IMAGE_NAME} --set image.tag=${TAG}'
                    }
                }
            }
        }
    }
}
