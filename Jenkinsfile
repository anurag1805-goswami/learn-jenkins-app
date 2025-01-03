#Leanrning Jenkins

pipeline {
    agent any
    
    environment {
        NETLIFY_SITE_ID = 'cea39989-36a0-4dea-8244-0844b74ed5c5'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        
        stage('Build') {
            agent{
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo 'installing running npm build and dependencies'
                sh '''
                ls -la
                node --version
                npm --version
                npm ci
                npm run build
                ls -la
                '''
                //node --version used to check the currently installed version of Node.js on your system.
                //npm ci command is used to install dependencies in a Node.js project

            }

        }

    

        stage('Tests') {
            parallel {
                stage('Unit Test') {
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
                    '''
                    }
                
                post {
                    always {
                        junit 'jest-results/junit.xml'
                    }
                }
            }

                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
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
                
                post { //Post section will be always there after the steps
                    always {
                        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                    }

                }
                
            }
         }
     }
        stage ('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                npm install netlify-cli
                node_modules/.bin/netlify --version
                echo "Deploying to production Site ID: $NETLIFY_SITE_ID"
                node_modules/.bin/netlify status
                 node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
 }

}



