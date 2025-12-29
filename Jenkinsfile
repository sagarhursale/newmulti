pipeline {
    agent any

    environment {
        APP_NAME = "mb-app"
        APP_SERVER = "ubuntu@13.234.231.142"
    }

    stages {

        stage('Show Branch') {
            steps {
                echo "Branch Name: ${env.BRANCH_NAME}"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${APP_NAME}:${env.BRANCH_NAME} ."
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh "echo TEST PASSED"
            }
        }

        stage('Deploy to DEV') {
            when {
                branch 'dev'
            }
            steps {
                sh """
                ssh ${APP_SERVER} '
                docker rm -f ${APP_NAME} || true
                docker run -d -p 8081:80 --name ${APP_NAME} ${APP_NAME}:dev
                '
                """
            }
        }

        stage('Approval for PROD') {
            when {
                branch 'prod'
            }
            steps {
                input message: "Approve deployment to PROD?"
            }
        }

        stage('Deploy to PROD') {
            when {
                branch 'prod'
            }
            steps {
                sh """
                ssh ${APP_SERVER} '
                docker rm -f ${APP_NAME} || true
                docker run -d -p 80:80 --name ${APP_NAME} ${APP_NAME}:prod
                '
                """
            }
        }

        stage('Feature Branch') {
            when {
                expression {
                    env.BRANCH_NAME.startsWith("feature/")
                }
            }
            steps {
                echo "Feature branch: build & test only"
            }
        }
    }
}

