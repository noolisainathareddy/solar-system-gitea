pipeline{
    agent any
    tools {
        nodejs 'nodejs21'
    }
    triggers {
        githubPush()  // triggers build on every push
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