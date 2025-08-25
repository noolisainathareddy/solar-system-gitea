pipeline{
    agent any
    environment{
        MONGO_URI="mongodb+srv://supercluster.d83jj.mongodb.net/superData"
        SONAR_QUBE_PATH= tool 'sonar-710'
        PATH="${env.PATH}:/Users/sainooli/.docker/bin"
    }
    tools{
        nodejs 'nodejs-24-4-1'
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
                catchError(buildResult: 'SUCCESS', message: 'OOps', stageResult: 'UNSTABLE') {
                     withSonarQubeEnv('sonar-qube-credentials'){
                         sh ' echo $SONAR_QUBE_PATH'
                         sh '''
                             $SONAR_QUBE_PATH/bin/sonar-scanner \
                               -Dsonar.projectKey=soalr-system \
                               -Dsonar.sources=app.js \
                               -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \
                         '''
                     }
                     waitForQualityGate abortPipeline: true
                }
            }
        }
        stage("Docker image"){
            steps{
                sh '''
                    echo $PATH
                    which docker
                    docker --version
                    docker build -t nooli/solar-system:${BUILD_NUMBER} .
                '''
            }
        }
        stage('push to hub'){
            steps{
                sh 'docker push nooli/solar-system:${BUILD_NUMBER}'
            }
        }
    }
}