pipeline {
    agent any

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
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                }
            }
            steps {
                echo 'Test'
                sh '''
                test -f build/index.html
                npm test
                '''
            }

      }
            
    }
// This section is after the stages 
    post {
        always {
            junit 'test-results/junit.xml'
        }
    }

  }


