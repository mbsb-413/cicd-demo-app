pipeline {
    agent any

    parameters {
        string(name: 'REPO_URL', defaultValue: 'https://github.com/mbsb-413/cicd-demo-app.git')
        string(name: 'GIT_BRANCH', defaultValue: 'master')
        booleanParam(name: 'RUN_TESTS', defaultValue: true)
    }

    stages {
        stage('Checkout') {
            steps {
                git url: params.REPO_URL, branch: params.GIT_BRANCH
                echo "Branch: ${params.GIT_BRANCH}"
                sh 'git rev-parse HEAD'
            }
        }

        stage('Validate') {
            steps {
                sh '''
                    test -f README.md
                    test -f requirements.txt
                    test -d src
                    test -d tests
                '''
            }
        }

        stage('Test') {
            when {
                expression { params.RUN_TESTS }
            }
            steps {
                sh 'python3 -m unittest discover -s tests -v'
            }
        }

        stage('Build') {
            steps {
                sh '''
                    echo "BUILD_NUMBER=$BUILD_NUMBER" > build-info.txt
                    echo "COMMIT_SHA=$(git rev-parse HEAD)" >> build-info.txt
                    tar -czf app.tar.gz src tests README.md requirements.txt build-info.txt
                '''
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'app.tar.gz'
            }
        }
    }

    post {
        success {
            echo 'Сборка завершена успешно'
        }
        failure {
            echo 'Сборка завершена с ошибкой'
        }
    }
}
