pipeline {
    agent any
    
    tools {
        maven 'maven3'
    }

    stages {
        stage ('Git Checkout') {
            steps {
                git branch: 'main' , url: 'https://github.com/Amittripathy901/Boardgame.git'
                    }
        }
        stage('Compile') {
            steps {
             sh 'mvn compile'
            }
        }
        stage('test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Package') {
            steps {
               sh 'mvn package'
            }
        }
        }
    }
}
