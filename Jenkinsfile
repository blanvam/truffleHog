#!groovy

pipeline {
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

        stage('Prepare environment') {
            steps {
                script {
                    sh(returnStdout: true, script: 'python3.6 -m venv env')
                    sh(returnStdout: true, script: 'source env/bin/activate')
                    sh(returnStdout: true, script: 'pip install -e .')
                }
            }
        }

        stage('Unit tests') {
            steps {
                script {
                    sh 'python3 manage.py unit_tests --clean'
                }
            }
        }

        stage('Functional tests') {
            steps {
                script {
                    sh 'echo "RUN YOUR FUNCTIONAL TEST"'
                }
            }
        }

        stage('Secret Review') {
            steps {
                script {
                    env.secretReview = sh(returnStdout: true, script: "trufflehog --regex ${env.gitCommit}").trim()
                }
                echo "Secret REVIEW: ${env.secretReview}"
            }
        }

        stage('Code Review Analyzer') {
            steps{
                script {
                  sh 'echo "SEND REVIEW NOTIFICATION"'
                }
            }
        }

        stage('Generate and PUSH image to Regitry') {
            steps {
                script {
                    sh 'echo "GENERATE YOUR IMAGE FROM YOUR CODE AND PUSH"'
                }
            }
        }

        stage('Image Review') {
            steps {
                script {
                    sh 'echo "Image Review"'
                }
            }
        }

    }
}
