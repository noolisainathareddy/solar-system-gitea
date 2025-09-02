pipeline{
    agent any
    tools {
        nodejs 'nodejs21'
    }
    stages {
        stage('first stage') {
            steps{
                echo "Starting"
                sh 'sudo apt install nodejs'
                sh 'sudo apt install npm'
            }
        }
    }
}