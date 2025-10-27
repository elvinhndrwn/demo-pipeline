pipeline {
    agent any

    tools {
        maven 'Maven3' // pastikan nama sesuai di Jenkins Global Tool Config
        jdk 'Java17'   // pastikan sudah setup JDK di Jenkins juga
    }

    environment {
        SONARQUBE_ENV = credentials('sonar-token') // optional jika pakai credentials
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/username/spring-boot-demo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeLocal') {
                    sh '''
                        mvn sonar:sonar \
                          -Dsonar.projectKey=spring-boot-demo \
                          -Dsonar.host.url=http://localhost:9000 \
                          -Dsonar.login=$SONARQUBE_ENV
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build and SonarQube Quality Gate passed!'
        }
        failure {
            echo '❌ Build or Quality Gate failed!'
        }
    }
}
