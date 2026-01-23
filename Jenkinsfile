pipeline {
    agent { label 'linuxgit' }
    
    options {
        timestamps()
    }
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

//        stage('Prepare tools') {
//            steps {
//                sh '''
//                    set -e
//
//                  sudo apt-get update
//                  sudo apt-get install -y \
//                        build-essential \
//                        python3 \
//                      python3-pip \
//                      pipx \
//                      cmake \
//                      curl
//
//                    pipx install cmakelint || true
//                '''
//            }
//        }

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

        stage('Build') {
            steps {
                echo 'Running build with CMake...'
                sh '''
                    if [ -f CMakeLists.txt ]; then
                        rm -rf build
                        mkdir build
                        cd build
                        cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=ON ..
                        make -j$(nproc)
                        cp compile_commands.json ..
                    else
                        echo "CMakeLists.txt not found!"
                        exit 1
                    fi
                '''
            }
        }

        stage('Unit Tests') {
            steps {
                sh '''
                    cd build
                    ctest --output-on-failure --test-output-junit test-results.xml || true
                '''
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: 'build/test-results.xml'
                }
            }
        }
    }
}
