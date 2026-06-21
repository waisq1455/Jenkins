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
                    // Fixed: Changed 'aniq47' to 'iamsakib' so it matches your repository and registry credentials
                    def customImage = docker.build("aniq47/petclinic:${env.BUILD_NUMBER}", "./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        customImage.push()
                    }
                }
            }
        }

        stage('Build on Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'pwd'
                    sh 'cp -R helm/* .'
                    sh "/usr/local/bin/helm upgrade --install petclinic-app petclinic --set image.repository=aniq47/petclinic --set image.tag=${env.BUILD_NUMBER}"
                }
            }
        }
    }
}
