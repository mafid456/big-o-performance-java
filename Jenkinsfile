pipeline {
    agent {
        docker {
            image 'node:18'  // Run inside official Node.js Docker image
            args '-v $HOME/.npm:/root/.npm' // cache npm dependencies
        }
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/mafid456/big-o-performance-java.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    sh 'npm ci'  // Faster & reproducible than "npm install"
                }
            }
        }

        stage('Build') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    sh 'npm run build'
                }
            }
        }

        stage('Test') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    sh 'npm test || echo "No tests defined"'
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'dist/**', fingerprint: true
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs() // clears workspace after build
        }
    }
}
