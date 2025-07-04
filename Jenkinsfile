pipeline {
    agent any
    
    tools {
        maven 'maven3'
    }


    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'feature', url: 'https://github.com/Amittripathy901/Boardgame.git'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }
    }
}
