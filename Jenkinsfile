pipeline {
    agent none
    stages {
        stage('build-frontend') {
            agent { docker { image 'node:20-bullseye' } }
            steps {
              script {
                updateGitlabCommitStatus name: 'build-frontend', state: 'running'
                sh 'npm install'
              }
            }
            post {
                failure {
                    updateGitlabCommitStatus name: 'build-frontend', state: 'failed'
                    script {
                        sh "cd ./src/ && git diff -- ."
                    }
                }
                success {
                  updateGitlabCommitStatus name: 'build-frontend', state: 'success'
                }
            }
        }
        stage('test-frontend') {
            agent { docker { image 'node:20-bullseye' } }
            steps {
              script {
                updateGitlabCommitStatus name: 'test-frontend', state: 'running'
                sh 'apt-get update'
                sh 'wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb'
                sh 'apt install -y ./google-chrome*.deb'
                sh 'export CHROME_BIN=/usr/bin/google-chrome'
                sh 'npm install -g @angular/cli@^17.3.8'
                sh 'npm install'
                sh 'npm run build'
                sh 'npm run test:ci'
              }
            }
            post {
                failure {
                    updateGitlabCommitStatus name: 'test-frontend', state: 'failed'
                    script {
                        sh "cd ./src/ && git diff -- ."
                    }
                }
                success {
                  updateGitlabCommitStatus name: 'test-frontend', state: 'success'
                }
                always {
                  junit 'unit-test-results.xml'
                }
            }
        }
    }
}
