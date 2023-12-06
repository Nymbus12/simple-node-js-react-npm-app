pipeline {
    agent {
        docker {
            image 'node:20.10.0-alpine3.18'
            args '-p 3000:3000'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Security Scans') {
            parallel {
                stage('Dastardly Scan') {
                    steps {
                        script {
                            // Pull Dastardly Docker image
                            sh 'docker pull public.ecr.aws/portswigger/dastardly:latest'
                            
                            // Run Dastardly scan
                            sh '''
                                docker run --user $(id -u) -v ${WORKSPACE}:${WORKSPACE}:rw \
                                -e BURP_START_URL=http://localhost:3000 \
                                -e BURP_REPORT_FILE_PATH=${WORKSPACE}/dastardly-report.xml \
                                public.ecr.aws/portswigger/dastardly:latest
                            '''
                        }
                    }
                }
                
            }
        }
        stage('Deliver') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
                input message: 'Finished using the web site? (Click "Proceed" to continue)' 
                sh './jenkins/scripts/kill.sh' 
            }
        }
    }
}

