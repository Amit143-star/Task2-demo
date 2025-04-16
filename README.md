# Task2-demo
Set up a basic Jenkins pipeline to automate the process of building and deploying an application.
jinkinsfiles :- 
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'yourdockerhubusername/your-app-name'
        TAG = 'latest'
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/your-username/your-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${TAG}")
                }
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.image("${IMAGE_NAME}:${TAG}").push()
                }
            }
        }

        stage('Deploy (Optional)') {
            steps {
                echo 'Deploy steps go here — e.g., Docker run on remote server'
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
    }
}
Dockerfile :-
# Use an official Node.js base image
FROM node:18-alpine

# Set working directory inside container
WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install --production

# Copy the rest of the application code
COPY . .

# Expose the port your app runs on
EXPOSE 8080

# Command to start the app
CMD ["node", "index.js"]
