pipeline {
    agent any

    tools {
        maven 'Maven3'   // Manage Jenkins > Tools me jo naam diya ho
        jdk 'JDK17'      // wahi naam jo tools me configure kiya ho
    }

    stages {
        
        stage('Build Backend') {
            steps {
                dir('polling-app-server') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Test Backend') {
            steps {
                dir('polling-app-server') {
                    sh 'mvn test'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('polling-app-client') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'polling-app-server/target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Build successful! ✅'
        }
        failure {
            echo 'Build failed! ❌ Check logs above.'
        }
    }
}