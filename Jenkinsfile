pipeline {
    agent any
    stages {
        stage('Build Maven') {
            steps {
                sh 'pwd'
                sh 'mvn clean install package'
            }
        }
        stage('Copy Artifacts') {
            steps {
                sh 'pwd'
                sh 'cp -r target/*.jar docker'
            }
        }
        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    def customImage = docker.build("aniq47/petclinic:${env.BUILD_NUMBER}", "./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        customImage.push()
                    }
                }
            }
        }
	stage ('build on kubernetes') {
	  steps
	     withKubeconfig([credentialsId: 'kubeconfig']) {
	     sh 'pwd'
	     sh 'cp -R helm/* .'
	     sh 'ls -1trh'
	     sh 'pwd'
	     sh '/usr/local/bin/heml upgrade --install petclinic-app petclinic --set image.repository=aniq47/petclinic --set image.tag=${BUILD_NUMBER}'
    }
}


