pipeline {
    agent any
    
    stages {
        stage("Code") {
            steps {
                git url: "https://github.com/ajitfawade/node-todo-cicd.git", branch: "master"
                echo "Code cloned !"
            }
        }
        
        stage("Build & Test") {
            steps {
                sh "docker build . -t node-app-todo"
                echo "docker build done"
            }
        }
        
        stage("Push to repository") {
            steps {
                withCredentials([usernamePassword(credentialsId: "dockerHub", passwordVariable: "dockerHubPass", usernameVariable: "dockerHubUser")]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker tag node-app-todo ${env.dockerHubUser}/node-app-todo:latest"
                    sh "docker push ${env.dockerHubUser}/node-app-todo:latest"
                }
                echo "Code pushed to repository"
            }
        }
        
        stage("Deploy") {
            steps {
                sh "docker-compose up -d"
                echo "Deployed to AWS EC2"
            }
        }
    }
}
