pipeline{
    agent any
    environment{
        PATH="${env.PATH}:/usr/local/bin"
    }
    tools {
        nodejs 'nodejs-24-4-1'
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
        stage('list s3') {
            steps{
                sh 'echo $PATH'
                    withAWS(profile: 'aws-kube', region: 'us-east-1') {
                        sh 'aws --version'
                        sh 'aws s3 ls'
                        dir('/Users/sainooli/.jenkins/workspace/solar'){
                            sh 'aws s3 cp test-results.xml s3://sainathchottu-test-bucket/'
                        }
                    }

            }
        }

    }
    post {
        always {
            slackSend channel: '#testkit-ai-dev', message: "Successful"
        }
    }
}