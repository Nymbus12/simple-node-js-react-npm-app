pipeline {
    agent any
    environment {
        LOCAL_PROJECT_URL = 'http://localhost:3000'
    }
    stages {
        stage('Build and Test') {
            parallel {
                stage('Build') {
                    agent {
                        docker {
                            image 'node:20.10.0-alpine3.18'
                            args '-p 3000:3000'
                        }
                    }
                    steps {
                        script {
                            sh 'npm install'
                        }
                    }
                }
                stage('Dastardly Scan') {
                    agent any
                    steps {
                        script {
                            // Add your Dastardly scan steps here
                            sh 'docker pull public.ecr.aws/portswigger/dastardly:latest'
                            sh '''
                                docker run --user $(id -u) -v ${WORKSPACE}:${WORKSPACE}:rw \
                                -e BURP_START_URL=${LOCAL_PROJECT_URL} \
                                -e BURP_REPORT_FILE_PATH=${WORKSPACE}/dastardly-report.xml \
                                public.ecr.aws/portswigger/dastardly:latest
                            '''
                        }
                        junit testResults: 'dastardly-report.xml', skipPublishingChecks: true
                    }
                }
            }
        }
        stage('Deliver') {
            steps {
                script {
                    sh './jenkins/scripts/deliver.sh'
                    input message: 'Finished using the web site? (Click "Proceed" to continue)'
                    sh './jenkins/scripts/kill.sh'
                }
            }
        }
    }
}

