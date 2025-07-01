pipeline {
    agent any
    
    tools {
        maven 'maven3'
    }

    stages {
        
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
