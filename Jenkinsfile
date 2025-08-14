pipeline{
    agent any
//     environments{
//
//     }
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
        stage('Install dependencies'){
            steps{
                sh 'npm install'
            }
        }
        stage{
            parallel{
                stage('Dependency vulnerability check'){
                    steps{
                        sh 'npm audit --audit-level=critical'
                    }
                 }
                stage('OWASP dependency check'){
                    steps{
                        sh '''
                            echo 'Dummy stage'
                            for((i=1; i<=10; i++))
                            do
                                echo "$i"
                                sleep 1
                            done
                        '''
                    }
                }
            }
        }
        stage('Dependency vulnerability check'){
            steps{
                sh 'npm audit --audit-level=critical'
            }
        }
        stage('OWASP dependency check'){
            steps{
                sh '''
                    echo 'Dummy stage'
                    for((i=1; i<=10; i++))
                    do
                        echo "$i"
                        sleep 1
                    done
                '''
            }
        }
        stage('Code Coverage'){
            steps{
                sh 'npm run coverage'
            }
        }
        stage("Docker image"){
            steps{
                sh "docker build -t nooli/solar-system ."
            }
        }
    }
}