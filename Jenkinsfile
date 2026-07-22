// pipeline {
//     agent any

//     tools {
//         maven 'Maven3'   // Manage Jenkins > Tools me jo naam diya ho
//         jdk 'JDK17'      // wahi naam jo tools me configure kiya ho
//     }

//     stages {
        
//         stage('Build Backend') {
//             steps {
//                 dir('polling-app-server') {
//                     sh 'mvn clean package -DskipTests'
//                 }
//             }
//         }

//         stage('Test Backend') {
//     steps {
//         dir('polling-app-server') {
//             sh 'mvn test -DskipTests'
//         }
//     }
// }

//         stage('Build Frontend') {
//             steps {
//                 dir('polling-app-client') {
//                     sh 'npm install'
//                     sh 'npm run build'
//                 }
//             }
//         }

//         stage('Archive Artifacts') {
//             steps {
//                 archiveArtifacts artifacts: 'polling-app-server/target/*.jar', fingerprint: true
//             }
//         }
//     }

//     post {
//         success {
//             echo 'Build successful! ✅'
//         }
//         failure {
//             echo 'Build failed! ❌ Check logs above.'
//         }
//     }
// }

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/<tumhara-username>/three-tier-jenkins-project.git'
            }
        }

        stage('Build Backend') {
            steps {
                dir('polling-app-server') {
                    sh 'mvn clean package -DskipTests'
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

        stage('Deploy Backend') {
            steps {
                dir('polling-app-server') {
                    sh '''
                        pkill -f "polls-0.0.1-SNAPSHOT.jar" || true
                        sleep 2
                        nohup java -jar target/polls-0.0.1-SNAPSHOT.jar > app.log 2>&1 &
                        sleep 10
                    '''
                }
            }
        }

        stage('Deploy Frontend') {
            steps {
                sh '''
                    sudo rm -rf /var/www/html/*
                    sudo cp -r polling-app-client/build/* /var/www/html/
                    sudo systemctl restart nginx
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    echo "Checking backend..."
                    curl -f http://localhost:8080/api/polls || echo "Backend check failed"
                    echo "Checking frontend..."
                    curl -f http://localhost/ || echo "Frontend check failed"
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Backend + Frontend Deployed Successfully!'
        }
        failure {
            echo '❌ Deployment Failed! Check console logs.'
        }
    }
}