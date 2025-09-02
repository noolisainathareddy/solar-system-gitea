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
                echo "first stage running"
                sh 'sudo apt install nodejs'
                sh 'sudo apt install npm'
            }
        }
    }
}