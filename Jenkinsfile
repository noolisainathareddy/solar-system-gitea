pipeline{
    agent any
    tools {
        nodejs 'nodejs21'
    }
    stages {
        stage('first') {
            steps{
                dir('solar-system-gitea'){
                    sh 'ls'
                    sh 'pwd'
                }
            }
        }
    }
}