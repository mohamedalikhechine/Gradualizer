pipeline {
    agent none

    options {
        ansiColor('xterm')
    }

    parameters {
        string(name: 'DOCKER_NAME', defaultValue: 'erlangsolutions/safe_dev:checkmarx_demo', description: 'Docker image to use for SAFE stage')
    }

    stage('Install Docker') {
        agent any
        steps {
            sh '''
                echo "Installing Docker..."
                sudo apt-get update
                sudo apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release
                curl -fsSL https://download.docker.com/linux/$(. /etc/os-release && echo "$ID")/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
                echo \
                  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/$(. /etc/os-release && echo "$ID") \
                  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
                sudo apt-get update
                sudo apt-get install -y docker-ce docker-ce-cli containerd.io
                docker --version
            '''
        }
    }
    
    stages {  
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
