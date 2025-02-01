pipeline {
    agent any   

    environment {
        NETLIFY_SITE_ID = "5490e0f8-a4f1-4913-8008-ea202c0fb918"
        NETLIFY_AUTH_TOKEN = credentials('netlify-token');
    }

    stages {
        stage('Build') {
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
        }
        stage('Test'){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }

            }
            steps{
                sh '''
                test -f build/index.html
                npm test
                '''
                
            }
        }
        stage('Deploy') {
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }

            }
            steps {
                sh '''
                echo "Trigger SCM changes"
                npm install netlify-cli
                node_modules/.bin/netlify --version
                echo "Deploying to production $NETLIFY_SITE_ID"
                node_modules/.bin/netlify status
                node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
        stage('Production End-To-End Testing') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }

            environment {
                CI_ENVIRONMENT_URL = 'https://incomparable-raindrop-0e3e34.netlify.app'
            }

            steps {
                sh '''
                    npx playwright test  --reporter=html
                '''
            }
        }
    }



    post{
        always {
            junit 'test-results/junit.xml'
        }
    }
}
