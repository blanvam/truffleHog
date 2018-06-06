#!groovy

pipeline {
    agent any
    options {
        buildDiscarder(logRotator([
            daysToKeepStr: '7',
            numToKeepStr: '10'
        ]))
    }
    stages {
        stage('Initialize pipeline variables') {
            steps {
                script {
                    env.versionToDeploy = sh(returnStdout: true, script: 'head -1 version.txt').trim()
                    env.giturl = sh(returnStdout: true, script: 'git config --get remote.origin.url').trim()
                    env.gitCommit = sh(returnStdout: true, script: 'git log --pretty=format:"%H - %cn, %ce %cd : %s" | head -1 | awk \'{ print $1 }\'').trim()
                    env.repositoryName = sh(returnStdout: true, script: 'git config --get remote.origin.url | awk -F "/" \'{ print $5}\' | sed \'s/.\\{4\\}$//\'').trim()
                    env.repositoryBranch = sh(returnStdout: true, script: "git branch -r --color=never --contains ${env.gitCommit} | tail -1 | sed \"s@origin/@@g\" | tr -d \" \" | tr -d \"*\"").trim()
                    env.commitInfo = sh(returnStdout: true, script: 'git log --pretty=format:"%H - %cn, %ce %cd : %s" | head -1').trim()
                }
                echo "REPOSITORY: ${env.giturl}"
                echo "REPOSITORY NAME: ${env.repositoryName}"
                echo "REPOSITORY BRANCH: ${env.repositoryBranch}"
                echo "COMMIT INFO: ${env.commitInfo}"
                echo "GIT COMMIT: ${env.gitCommit}"
                echo "VERSION TO DEPLOY: ${env.versionToDeploy}"
            }
        }

        stage('Unit tests') {
            steps {
                withPythonEnv('python3.5') {
                    script {
                        pysh 'pip3 install -r requirements.txt'
                        pysh 'pip install -e .'
                        sh 'python3.5 test_all.py'
                    }
                }
            }
        }

        stage('Functional tests') {
            steps {
                script {
                    echo "RUN YOUR FUNCTIONAL TEST"
                }
            }
        }

        stage('Secret Review') {
            steps {
                script {
                    echo "---BEGIN---"
                    try {
                        sh("trufflehog --json --regex ${env.giturl}")
                    } catch (err) {
                        echo "Caught: ${err}"
                        currentBuild.result = 'SUCCESS'
                    }
                    echo "---END---"
                }
            }
        }

        stage('Code Review Analyzer') {
            steps{
                script {
                  echo "SEND REVIEW NOTIFICATION"
                }
            }
        }

        stage('Generate and PUSH image to Regitry') {
            steps {
                script {
                    echo "GENERATE YOUR IMAGE FROM YOUR CODE AND PUSH"
                }
            }
        }

        stage('Image Review') {
            steps {
                script {
                    echo "Image Review"
                }
            }
        }

    }

    post {
        failure {
            echo 'FAILURE'
        }
        unstable {
            echo 'UNSTABLE'
        }
        success {
            echo 'SUCCESS'
        }
        always {
            echo '#### CLEANNING ####'
        }
    }
}
