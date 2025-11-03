pipeline {
    agent any

    environment {
        SONAR_HOST_URL = "http://sonarqube:9000"
        BUCKET = "my-gcs-bucket"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool name: 'SonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    withSonarQubeEnv('sonar') {
                        sh """
                            export SONAR_SCANNER_OPTS="-Xmx1024m"
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=my-project-key \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=$SONAR_HOST_URL \
                            -Dsonar.login=$SONAR_AUTH_TOKEN
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Prepare files for Hadoop') {
            steps {
                script {
                    sh '''
                        BUILD_ID=${BUILD_NUMBER}
                        gsutil -m cp -r . gs://${BUCKET}/repo-${BUILD_ID}/files/
                        gsutil cp hadoop/mapper.py gs://${BUCKET}/mapper.py
                        gsutil cp hadoop/reducer.py gs://${BUCKET}/reducer.py
                        echo "Uploaded files to GCS. Run Hadoop job manually if needed."
                    '''
                }
            }
        }
    }
}
