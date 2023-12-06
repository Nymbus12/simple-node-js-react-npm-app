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
                            // Run Dastardly scan here
                            sh './jenkins/scripts/dastardly.sh'
                        }
                    }
                }
                stage('Other Security Scans') {
                    steps {
                        // Add other security scans here
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

