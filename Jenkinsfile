pipeline {
    agent any

    environment {
        SAFE_CLI_URL = 'https://safe-releases.s3.eu-central-1.amazonaws.com/safe_cli-1.0.1.tar.gz'
        SAFE_CLI_TAR = 'safe_cli-1.0.1.tar.gz'
        SAFE_LICENSE = credentials('SAFE_LICENSE')
        SAFE_CI_CONFIG_PATH = "${WORKSPACE}/.safe/config.json"
    }

    stages {
        stage('Build with Rebar3') {
            steps {
                echo 'Compiling project with Rebar3...'
                sh 'rebar3 compile'
            }
        }

        stage('Archive Build Artifacts') {
            steps {
                echo 'Archiving build artifacts...'
                stash includes: '_build/**', name: 'build-artifact'
            }
        }

        stage('SAFE Security Check') {
            steps {
                echo 'Running SAFE CLI for security scanning...'
                sh '''
                    curl -L -o $SAFE_CLI_TAR $SAFE_CLI_URL
                    mkdir -p safe
                    tar -xzf $SAFE_CLI_TAR -C safe
                    chmod +x safe/bin/safe_cli
                    ./safe/bin/safe_cli start

                    curl -X POST -H "Content-Type: application/json" -d @Gradualizer.safe https://safe-on-prem.fly.dev/api/reports
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
