pipeline{
    agent any
    environment{
        MONGO_URI="mongodb+srv://supercluster.d83jj.mongodb.net/superData"
    }
    tools{
        nodejs 'nodejs-24-4-1'
        hudson.plugins.sonar.SonarRunnerInstallation 'sonar-710'
    }
    stages{
        stage('Check node versions'){
            options{ timestamps() }
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
        stage('Dependency validation'){
            parallel{
                stage('Dependency vulnerability check'){
                    steps{
                        catchError(buildResult: 'SUCCESS', message: 'Vulnerability found') {
                            sh 'npm audit --audit-level=critical'
                        }
                    }
                 }
                stage('OWASP dependency check'){
                    steps{
                        sh '''
                            echo 'Dummy stage'
                            for((i=1; i<=10; i++))
                            do
                                echo "$i"
                            done
                        '''
                    }
                }
            }
        }
        stage('npm test'){
            options{ retry(2) }
            steps{
                withCredentials([usernamePassword(credentialsId: 'Mongo Credentialss', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                    sh 'npm test'
                }

            }
        }
        stage('Code Coverage'){
            steps{
                catchError(buildResult: 'SUCCESS', message: 'Oops! It is an error.', stageResult: 'UNSTABLE') {
                    sh 'npm run coverage'
                }
            }
        }
        stage('SonarQube'){
            steps{
                sh '''
                    sonar-scanner \
                      -Dsonar.projectKey=soalr-system \
                      -Dsonar.sources=. \
                      -Dsonar.host.url=http://localhost:9000 \
                      -Dsonar.login=sqp_7d4fc1424887b17ad4fc7f18a2093e4d01b19e69
                '''
            }
        }
        stage("Docker image"){
            steps{
                sh "docker build -t nooli/solar-system ."
            }
        }
    }
}