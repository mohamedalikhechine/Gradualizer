pipeline {
    agent any

    environment {
        SAFE_CLI_URL = 'https://safe-releases.s3.eu-central-1.amazonaws.com/safe_cli-1.0.1.tar.gz'
        SAFE_CLI_TAR = 'safe_cli-1.0.1.tar.gz'
        SAFE_LICENSE = credentials('SAFE_LICENSE')
        SAFE_CI_CONFIG_PATH = "${WORKSPACE}/.safe/config.json"
    }

    stages {
        /*
        stage('Compile') {
            steps {
                sh 'rebar3 compile'
            }
        }

        stage('Archive Build') {
            steps {
                stash includes: '_build/**', name: 'build-artifact'
            }
        }*/

        stage('SAFE Security Check') {
            steps {
                sh '''
                    curl -L -o $SAFE_CLI_TAR $SAFE_CLI_URL
                    mkdir -p safe
                    tar -xzf $SAFE_CLI_TAR -C safe
                    chmod +x safe/bin/safe_cli
                    ./safe/bin/safe_cli start
                '''
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
