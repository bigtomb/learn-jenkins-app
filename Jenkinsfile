pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '9140a65b-1150-42cf-ab62-d51be0fda44e'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        CI_ENVIRONMENT_URL = 'https://bigtomb-learn-jenkins.netlify.app/'

    }

    stages {
        
        stage('Build') {
            agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo 'Small Change'
                    npm ci
                    npm run build
                '''
            }
        }     
        stage('Tests'){
            parallel{
                stage('Unit Test'){
                    agent {
                        docker {
                            image 'node:20-alpine'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            echo "Test stage"
                            test -f build/index.html
                            npm test
                        '''
                    }
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }       
                }

                stage('E2E'){
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.58.0-noble'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }
                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Local Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }    
                }
            }
        }
        stage('Deploy Staging') {

            agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                   npm install netlify-cli@20.1.1
                   node_modules/.bin/netlify --version
                   node_modules/.bin/netlify status
                   node_modules/.bin/netlify deploy --dir=build

                '''
            }
        }
        stage('Deploy Prod') {
            agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                   npm install netlify-cli@20.1.1
                   node_modules/.bin/netlify --version
                   node_modules/.bin/netlify status
                   node_modules/.bin/netlify deploy --dir=build --prod

                '''
            }
        }
        stage('Prod E2E'){
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.58.0-noble'
                            reuseNode true
                        }
                    }
                    environment {
                    CI_ENVIRONMENT_URL = 'https://bigtomb-learn-jenkins.netlify.app'
                    }

                    steps {
                        sh '''
                            npx playwright test --reporter=html
                        '''
                    }
                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML E2E Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }    
                }
    }
}
