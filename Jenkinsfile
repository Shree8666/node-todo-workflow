pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'shree8666/node-todo-cicd:latest'
        SONARQUBE_SERVER = 'MySonarQube' // Name configured in Jenkins (Manage Jenkins > Configure > SonarQube)
        SONARQUBE_PROJECT_KEY = 'node-todo-app'
        DOCKER_CREDENTIALS_ID = 'docker-hub-cred' // Jenkins credential ID for Docker Hub
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/Shree8666/node-todo-cicd.git', branch: 'master'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    sh '''
                    sonar-scanner \
                      -Dsonar.projectKey=${SONARQUBE_PROJECT_KEY} \
                      -Dsonar.sources=. \
                      -Dsonar.host.url=http://172.28.0.1:9000
                    '''
                }
            }
        }
        
         stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {  // Adjust timeout as needed
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push ${DOCKER_IMAGE}
                    '''
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop your-app || true
                docker rm your-app || true
                docker run -d --name your-app -p 8000:8000 ${DOCKER_IMAGE}
                '''
            }
        }
    }
}
