pipeline {
    agent any

    environment {
        REPO_URL = "https://github.com/beast610/auto.git"
        MAIN_BRANCH = "main"
        DEV_BRANCH = "feature-branch"
        NOTIFY_EMAIL = "developer@example.com"
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    sh 'git clone ${REPO_URL} repo'
                    dir('repo') {
                        sh 'git checkout ${DEV_BRANCH}'
                    }
                }
            }
        }

        stage('Merge to Main') {
            steps {
                script {
                    dir('repo') {
                        sh 'git fetch origin'
                        def mergeStatus = sh(script: 'git merge origin/${MAIN_BRANCH}', returnStatus: true)
                        
                        if (mergeStatus == 0) {
                            echo "Merge Successful"
                            sh 'git push origin ${MAIN_BRANCH}'
                        } else {
                            echo "Merge Conflict Detected"
                            sh 'git merge --abort'
                            error "Merge Conflict"
                        }
                    }
                }
            }
        }

        stage('Notify Developer on Conflict') {
            when {
                failed()
            }
            steps {
                script {
                    def lastCommitter = sh(script: "git log -1 --pretty=format:'%an <%ae>'", returnStdout: true).trim()
                    emailext subject: "Merge Conflict Alert",
                             body: "Merge conflict occurred. Developer responsible: ${lastCommitter}",
                             to: "$NOTIFY_EMAIL"
                }
            }
        }
    }
}

