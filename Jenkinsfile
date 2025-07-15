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

        stage('Test'){

             agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true

                }
            }

            steps{
                sh'''
                test -f build/index.html
                npm test 

                '''

            }
        }

        stage('E2E '){

             agent{
                docker{
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                    args '-u root:root'

                }
            }

            steps{
                sh'''

                npm ci
                    npm run build
                    npm run serve -- --no-clipboard &
                    sleep 5 # Wait for serve to start
                    npx playwright test --reporter=html
                #npm install -g serve
                #node_modules/.bin/serve -s build
                #npx playwright test

                '''

            }
        }
    }
    post{
        always{
            junit 'jest-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }


}