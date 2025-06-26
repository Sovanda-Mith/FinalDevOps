pipeline {
    agent any

    environment {
        COMPOSER = 'composer'
        PHP = 'php'
        NPM = 'npm'
        APP_DIR = 'CICD_Mith_Sovanda'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Sovanda-Mith/FinalDevOps.git'
            }
        }

        stage('Install PHP Dependencies') {
            steps {
                dir("${APP_DIR}") {
                    sh "${COMPOSER} install --no-interaction --prefer-dist --optimize-autoloader"
                }
            }
        }

        stage('Install JS Dependencies & Build') {
            steps {
                dir("${APP_DIR}") {
                    sh """
                        ${NPM} install
                        ${NPM} run build
                    """
                }
            }
        }

        stage('Run Laravel Tests') {
            steps {
                dir("${APP_DIR}") {
                    sh "${PHP} artisan test --env=testing"
                }
            }
        }

        stage('Deploy with Ansible') {
            steps {
                dir("${APP_DIR}/ansible") {
                    sh "ansible-playbook -i inventory.ini playbook.yml"
                }
            }
        }
    }

    post {
        success {
            mail to: 'sovandam6@gmail.com',
                 cc: 'srengty@gmail.com',
                 subject: "✅ Laravel Build & Deploy Success: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Your Laravel app was successfully built and deployed.\n\nView Build: ${BUILD_URL}"
        }

        failure {
            script {
                def committerEmail = sh(
                    script: "cd ${APP_DIR} && git log -1 --pretty=format:'%ae'",
                    returnStdout: true
                ).trim()

                mail to: "sovandam6@gmail.com, ${committerEmail}",
                     cc: 'srengty@gmail.com',
                     s
