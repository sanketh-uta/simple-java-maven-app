pipeline {
    agent {
        docker {
            image 'maven:3.9.5-eclipse-temurin-17-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
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

        // stage('Deploy') {
        //     steps {
        //         script {
        //             // Add your deployment commands here
        //             echo 'Deploying the application...'
        //             // Example: sh 'kubectl apply -f deployment.yaml'
        //         }
        //     }
        // }
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
