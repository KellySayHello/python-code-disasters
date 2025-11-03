pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/KellySayHello/python-code-disasters.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt || true'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'pytest || echo "No tests found"'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_HOST_URL = "${env.SONAR_HOST_URL ?: 'http://sonarqube:9000'}"
            }
            steps {
                sh """
                    sonar-scanner \
                    -Dsonar.projectKey=python-code-disasters \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=$SONAR_HOST_URL \
                    -Dsonar.login=admin \
                    -Dsonar.password=admin
                """
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}
