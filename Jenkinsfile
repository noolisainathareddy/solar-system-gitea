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
                script{
                    def datePart = sh(script: "date +%y.%m", returnStdout: true).trim()
                    env.RELEASE_NUMBER = "${datePart}.${BUILD_NUMBER}"
                }
                sh '''
                    echo $PATH
                    which docker
                    docker --version
                    docker build -t nooli/solar-system:${RELEASE_NUMBER} .
                '''
            }
        }
        stage('push to hub'){
            steps{
                script{
                   sh "docker push nooli/solar-system:${RELEASE_NUMBER}"
                }
            }
        }
        stage('update image in kube repo'){
            steps{
                sh 'git clone git@github.com:noolisainathareddy/Basic-Python-App.git'
                dir('Basic-Python-App/kubernetes'){
                    sh '''
                        git checkout main
                        uname -a
                        sed -i '' "s|nooli/solar-system:[^[:space:]]*|nooli/solar-system:${RELEASE_NUMBER}|g" deployment.yaml
                        git add deployment.yaml
                        git commit -m "updated image name"
                        git push
                    '''
                    }
            }
        }
    }
    post{
        always{
            cleanWs()
        }
    }
}