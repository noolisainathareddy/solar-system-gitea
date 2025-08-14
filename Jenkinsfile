pipeline{
    agent any
    tools{
        nodejs 'nodejs-24-4-1'
    }
    stages{
        stage('Check node versions'){
            steps{
                sh 'node --version'
                sh 'npm --version'
            }
        }
    }
}