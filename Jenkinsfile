pipeline {
    agent any

    environment {
        IMAGE = "medalichrayta/student-management"
    }

    stages {

        stage('Clone') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Dalychrayta/devops.git'
            }
        }

        stage('Build Application') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw -DskipTests clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                    ./mvnw sonar:sonar \
                      -Dsonar.projectKey=student-management \
                      -Dsonar.projectName=student-management
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry(
                        'https://registry.hub.docker.com',
                        'dockerhub-credentials'
                    ) {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }
    }
}
