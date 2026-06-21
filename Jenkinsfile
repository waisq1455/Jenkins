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
                // Fixed: Added the missing closing quote for the cp command
                sh 'cp -r target/*.jar docker/'
            }
        }
    }
}
