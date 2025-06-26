pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Sovanda-Mith/FinalDevOps.git'
            }
        }

        stage('Debug Workspace') {
            steps {
                sh 'pwd && ls -lah'
            }
        }

        stage('Install PHP Dependencies') {
            steps {
                sh "composer install --no-interaction --prefer-dist --optimize-autoloader"
            }
        }

        stage('Install Node.js & npm') {
            steps {
                sh '''
                    if ! command -v npm >/dev/null 2>&1; then
                        echo "Installing Node.js and npm..."
                        curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
                        sudo apt-get install -y nodejs
                    else
                        echo "Node.js already installed."
                    fi
                    node -v
                    npm -v
                '''
            }
        }

        stage('Install JS Dependencies & Build') {
            steps {
                sh """
                    npm install
                    npm run build
                """
            }
        }

        stage('Run Laravel Tests') {
            steps {
                sh "php artisan test tests/Unit/ExampleTest.php"
            }
        }

        stage('Deploy with Ansible') {
            steps {
                dir('ansible') {
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
                    script: "git log -1 --pretty=format:'%ae'",
                    returnStdout: true
                ).trim()

                mail to: "sovandam6@gmail.com, ${committerEmail}",
                     cc: 'srengty@gmail.com',
                     subject: "❌ Laravel Build/Deploy Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                     body: """Hello ${committerEmail},

Your recent commit appears to have caused the build or deployment to fail.

🔧 Job: ${env.JOB_NAME}
🔢 Build: ${env.BUILD_NUMBER}
🔗 Console Output: ${env.BUILD_URL}console

Please review the build logs and address the issue.

Regards,
Jenkins CI
"""
            }
        }
    }
}
