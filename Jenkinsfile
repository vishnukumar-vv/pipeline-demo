pipeline {
    agent { label 'linuxgit' }
    
    environment {
        GIT_REPO = 'https://github.com/vishnukumar-vv/pipeline-demo.git'
        BRANCH = 'main'
    }
    
    stages {
        stage('Checkout repo') {
            steps {
                git branch: BRANCH,
                    url: GIT_REPO,
                    credentialsId: 'b4efd010-b24e-4c56-b027-316b1cdbbba4'
            }
        }
        stage('Prepare tools') {
            steps {
                echo 'Installing required tools on ubuntu...'
                sh '''
                    set -e
                    
                    sudo apt-get update -y
                    
                    sudo apt-get install -y \
                        build-essential \
                        python3 \
                        cmake \
                        python3-pip \
                        curl
                    pip3 install --quiet cmakelint
                '''
            }
        }
        stage('Lint') {
            steps {
                echo 'Running lint checks'
                sh '''
                    if [ -f CMakeLists.txt ]; then 
                        cmakelint CMakeList.txt > lint_report.txt || true
                    else 
                        echo "CMakeLists.txt is not found"
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
