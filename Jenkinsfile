pipeline {
    agent any 
    tools {
        maven 'maven'
    }

    environment {
        GCP_CREDENTIALS=credentials('creds-gcloud') 
        GCP_PROJECT='jenkins-demo-405221'
        GCE_INSTANCE_NAME='jenkins-instance1'
        GCP_ZONE='us-central1-a'
        STORAGE_BUCKET='jenkins-bucket-demo'
    }

    stages {
        stage('Build') {
            steps {
                script {
                    def mavenHome=tool 'maven'
                    env.PATH="${mavenHome}/bin:${env.PATH}"

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

        stage('Deploy to cloud storage') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'creds-gcloud', variable: 'GCP_CREDENTIALS_FILE')]) {
                        sh "gcloud auth activate-service-account --key-file=${GCP_CREDENTIALS_FILE}"
                    }

                    sh "gsutil cp -r target gs://${STORAGE_BUCKET}/"
                }
            }
        }

        stage('Download from Google Cloud Storage') {
            steps {
                script {
                    // Download the JAR file from Google Cloud Storage to the workspace
                    sh "gsutil cp gs://${STORAGE_BUCKET}/target/my-app-1.0-SNAPSHOT.jar ./"
                }
            }
        }

        stage('Deploy to Google Compute Engine') {
            steps {
                script {

                    // Authenticate with Google Cloud Platform using service account credentials
                    withCredentials([file(credentialsId: 'gcloud-creds', variable: 'GOOGLE_CREDENTIALS_FILE')]) {
                        sh "gcloud auth activate-service-account --key-file=${GOOGLE_CREDENTIALS_FILE}"
                    }

                    // Deploy to Google Compute Engine using gcloud commands
                    sh "gcloud config set project ${GCP_PROJECT}"

                    // Copy the JAR file to the Compute Engine instance
                    sh "gcloud compute scp --zone=${GCP_ZONE} my-app-1.0-SNAPSHOT.jar ${GCE_INSTANCE_NAME}:~/"
                    // SSH into the instance and start the application
                    sh "gcloud compute ssh --zone=${GCP_ZONE} ${GCE_INSTANCE_NAME} --command 'nohup java -jar ~/my-app-1.0-SNAPSHOT.jar &'"
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
