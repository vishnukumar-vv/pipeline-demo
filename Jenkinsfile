pipeline {
    agent { label 'linuxgit' }

    environment {
        GIT_REPO = 'https://github.com/vishnukumar-vv/pipeline-demo.git'
        BRANCH   = 'main'
    }

    stages {

        stage('Checkout repo') {
            steps {
                git branch: BRANCH,
                    url: GIT_REPO,
                    credentialsId: 'gitHub_id'
            }
        }

        stage('Prepare tools') {
            steps {
                sh '''
                    set -e

                    sudo apt-get update
                    sudo apt-get install -y \
                        build-essential \
                        python3 \
                        python3-pip \
                        pipx \
                        cmake \
                        curl

                    pipx ensurepath
                    pipx install cmakelint
                '''
            }
        }

        stage('Lint') {
            steps {
                echo 'Running lint checks'
                sh '''
                    if [ -f CMakeLists.txt ]; then
                        ~/.local/bin/cmakelint CMakeLists.txt > lint_report.txt || true
                    else
                        echo "CMakeLists.txt not found" > lint_report.txt
                    fi
                '''
            }
            post {
                always {
                    archiveArtifacts artifacts: 'lint_report.txt', fingerprint: true
                }
            }
        }
    }
}
