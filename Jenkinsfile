pipeline {
    agent any
     tools {
        maven 'maven'
    }
    environment {
        GCP_CREDENTIALS = credentials('creds-gcloud')
    }

    stages {
        stage('Build') {
            steps {
                script {
                    def mavenHome = tool 'maven'
                    env.PATH = "${mavenHome}/bin:${env.PATH}"

                    // Run Maven build
                    sh 'mvn -B -DskipTests clean package'
                }
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deliver') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'creds-gcloud', variable: 'GCP_CREDENTIALS_FILE')]) {
                        sh "gcloud auth activate-service-account --key-file=${GCP_CREDENTIALS_FILE}"
                    }
                    
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
