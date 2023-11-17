pipeline {
    agent {
        docker {
            image 'maven:3.9.5-eclipse-temurin-17-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    
    environment {
        GCP_CREDENTIALS = credentials('jenkins-demo')
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
                    withCredentials([file(credentialsId: 'jenkins-demo', variable: 'GCP_CREDENTIALS_FILE')]) {
                        sh '''
                            gcloud auth activate-service-account --key-file=${GCP_CREDENTIALS_FILE}
                            gcloud config set project jenkins-demo-405221
                            # Additional GCP commands for deployment
                            ./jenkins/scripts/deliver.sh
                        '''
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
