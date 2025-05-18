pipeline {
    agent any 
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerHub') // Matches your credentialsId
        COMPOSE_PROJECT_NAME = 'jenkins-notesapp'
    }
    stages {
        stage('Clone Code') {
            steps {
                echo 'Cloning the code'
                git url: 'https://github.com/rvmeesa/django-notes-app.git', branch: 'main', credentialsId: 'github-credentials'
            }
        }
        stage('Build') {
            steps {
                echo 'Building the image'
                sh 'docker build -t my-note-app .'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing the image to docker hub'
                withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPass', usernameVariable: 'dockerHubUser')]) {
                    sh 'docker tag my-note-app ${dockerHubUser}/my-note-app:latest'
                    sh 'docker login -u ${dockerHubUser} -p ${dockerHubPass}'
                    sh 'docker push ${dockerHubUser}/my-note-app:latest'
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying the container'
                sh 'docker-compose -p $COMPOSE_PROJECT_NAME -f docker-compose-jenkins.yml down || true'
                sh 'docker-compose -p $COMPOSE_PROJECT_NAME -f docker-compose-jenkins.yml up -d'
            }
        }
    }
}