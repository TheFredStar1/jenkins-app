pipeline {
    agent {
        docker {
            image 'node:18-alpine'
            reuseNode
        }
    }

    stages {
        stage('Build') {
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                '''
            }
        }
        stage('test') {
            steps {
                sh '''
                    echo 'Testing...'
                    npm --version
                '''
            }
        }
    }
}
