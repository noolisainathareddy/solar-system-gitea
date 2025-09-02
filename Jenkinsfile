pipeline{
    agent any
    tools {
        nodejs 'nodejs21'
    }
    stages {
        stage('first') {
            steps{
                dir('solar-system-gitea'){
                    sh 'npm --version'
                    sh 'node --version'
                }
            }
        }
        stage('install and test') {
            steps{
                sh 'npm install'
                sh 'npm test'
            }
        }
    }
}