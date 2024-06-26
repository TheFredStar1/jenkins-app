pipeline {
    // Global docker agent
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
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

        stage('Run Tests') {
             // Run stages in parallel
            parallel {
                stage('Test') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            echo 'Test Stage'
                            npm --version
                            test -f 'build/index.html'
                            npm test
                        '''
                    }
                    post {
                        always {
                            // Specify where JUnit file exists
                            junit 'jest-results/junit.xml'
                          
                        }   
                    }
                }
                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.44.0-jammy'
                            reuseNode true
                        }
                    }
                    // We are directly calling node_modules/.bin/serve here instead of calling
                    // 'serve' directly because we are installing the serve package locally
                    // because of EAACCESS issues

                    // node_modules/.bin/serve -s build &
                    // the & and the sleep right after
                    // We use the '&' so that serve runs in the background
                    // and then sleep 10 seconds to allow the server to start before running our playwright test
                    steps {
                        sh '''
                            echo 'Running E2E'
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright install chromium
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }
                    post {
                        always {
                            // Generated with Jenkins Snippet Generator
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }   
                    }
                }
            }
        }
    }
}
