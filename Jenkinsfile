pipeline {
    agent any

    tools {
        nodejs "nodejs"   // Make sure Jenkins has NodeJS installed & configured with this name
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/mafid456/big-o-performance-java.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
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
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build and tests successful!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}

