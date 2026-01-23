pipeline {
    agent any

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
                    npm ci
                    npm run build
                '''
            }
        }

        stage('Test'){
            agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    echp "Test stage"
                    test build/index.html
                    npm test
                '''
            }
        }
    }
}
