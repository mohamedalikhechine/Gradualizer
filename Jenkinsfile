pipeline {
    agent none

    options {
        ansiColor('xterm')
    }

    stages {
        stage('Compile') {
            agent {
                docker {
                    image 'erlang:26.2.1'
                    args '-u root'
                }
            }
            steps {
                sh 'rebar3 compile'
            }
        }

        stage('Archive Build') {
            agent any
            steps {
                stash includes: '_build/**', name: 'build-artifact'
            }
        }

        stage('SAFE Security Check') {
            agent {
                docker {
                    image "safe"
                    args '-e SAFE_LICENSE'
                }
            }
            steps {
                sh 'safe start'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build and tests succeeded!'
        }
        failure {
            echo 'Something went wrong...'
        }
    }
}
