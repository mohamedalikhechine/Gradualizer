pipeline {
    agent none

    options {
        ansiColor('xterm')
    }

    parameters {
        string(name: 'DOCKER_NAME', defaultValue: 'erlangsolutions/safe_dev:checkmarx_demo', description: 'Docker image to use for SAFE stage')
    }
    
    stages {  
        
        stage('Install Docker') {
            agent any
            steps {
                sh '''
                    docker --version
                '''
            }
        }
        stage('Compile') {
            agent {
                docker {
                    image 'erlang:26.2.1'
                    args '-u root --privileged'
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
                    image "${params.DOCKER_NAME}"
                    args '-e SAFE_LICENSE --privileged'
                }
            }
            steps {
                sh 'echo "SAFE_LICENSE=$SAFE_LICENSE"' // for debug (optional)
                sh 'safe analyse'
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
