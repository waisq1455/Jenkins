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
                    // Fixed: Changed $(env.BUILD_NUMBER} to ${env.BUILD_NUMBER} and matched quotes
                    def customImage = docker.build("aniq47/petclinic:${env.BUILD_NUMBER}", "./docker")
                    
                    // Fixed: Cleaned up the mixed single/double quotes around registry setup
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        customImage.push()
                    }
                }
            }
        }
    }
}
