pipeline {
    // Global docker agent
    agent {
        docker {
            image 'node:18-alpine'
            reuseNode true
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
        stage('Test') {
            steps {
                sh '''
                    echo 'Test Stage'
                    npm --version
                    // Test to see if build/index.html exists
                    test -f 'build/index.html'
                    npm test
                '''
            }
        }
        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.44.0-jammy'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo 'Running E2E'
                '''
            }
        }
    }

    post {
        always {
		        // Specify where JUnit file exists
            junit 'test-results/junit.xml'
        }
    }
}
