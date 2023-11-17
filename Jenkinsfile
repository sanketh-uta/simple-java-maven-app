pipeline {
    agent any
    
    environment {
        GCP_CREDENTIALS = credentials('creds-gcloud')
    }

    stages {
        stage('Build') {
            steps {
                script {
                    sh 'mvn -B -DskipTests clean package'
                }
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Deliver') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'creds-gcloud', variable: 'GCP_CREDENTIALS_FILE')]) {
                        sh "gcloud auth activate-service-account --key-file=${GCP_CREDENTIALS_FILE}"
                    }
                    ./jenkins/scripts/deliver.sh
                }
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline succeeded! Triggering deployment...'
            // Add any post-success actions here
        }
        failure {
            echo 'Pipeline failed! Not triggering deployment.'
            // Add any post-failure actions here
        }
    }
}
