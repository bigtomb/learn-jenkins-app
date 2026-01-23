pipeline {
    agent any

    stages {
        agent docker{
            image node:20-alpine
        }
        stage('Build') {
            steps {
                sh '''
                    npm ci
                    npm run build
                '''
            }
        }
    }
}
