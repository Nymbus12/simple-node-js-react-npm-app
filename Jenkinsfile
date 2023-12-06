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

        stage('Dastardly Scanning') {
            steps {
                sh 'docker run -it -p 4000:4000 -v $(pwd):/app dastardly/dastardly dastardly scan --url http://localhost:3000'
            }
        }

        stage('Test') {
            steps {
                input message: 'Finished using dastardly scanning? (Click "Proceed" to continue)'
                sh './jenkins/scripts/test.sh'
            }
        }

        stage('Deliver') { 
            steps {
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/deliver.sh'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}
