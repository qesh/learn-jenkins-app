pipeline {
    agent any

    stages {
        /*stage('Build') {
            agent{
                docker{
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
                    ls -la

                '''
            }
        }*/

        stage('Tests') {
            parallel {
                stage('Unit test') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            #test -f build/index.html
                            npm test
                            ls -la test-results/ # Debug Jest test report
                        '''
                    }
                }
                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                            args '-u root:root'
                        }
                    }
                    steps {
                        sh '''
                            npm ci
                            npm run build
                            npm run serve -- --no-clipboard &
                            sleep 5
                            npx playwright test --reporter=html,junit --reporter-junit-export=jest-results/junit-playwright.xml
                            ls -la test-results/ # Debug Playwright test report
                            kill $SERVE_PID || true # Terminate serve process
                        '''
                    }
                }
            }
        }
    }
    post {
        always {
            junit 'jest-results/junit.xml'
            publishHTML([allowMissing: false, 
            alwaysLinkToLastBuild: false, 
            icon: '', 
            keepAll: false, 
            reportDir: 'playwright-report', 
            reportFiles: 'index.html', 
            reportName: 'Playwright HTML Report', 
            reportTitles: '', 
            useWrapperFileDirectly: true])
        }
    }
}