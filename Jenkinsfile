pipeline {
    agent any
    tools {
        jdk 'jdk-17'
        maven 'maven3'
        nodejs 'Node-24'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        RECIPIENT_EMAIL = 'amittripathy.work@gmail.com' // Replace with your actual email ID
        SENDER_EMAIL = 'jenkins@amittripathy.com' // Optional: Configure Jenkins to use a specific sender email
    }
    stages {
        stage('git-checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Amittripathy901/Boardgame.git'
            }
        }
        stage('Code-Compile') {
            steps {
                sh "mvn clean compile"
            }
        }
        stage('Unit-Test') {
            steps {
                sh "mvn clean test"
            }
        }
        stage('Trivy Scan') {
            steps {
                sh "trivy fs ."
            }
        }
        stage('OWASP Dependency Check') {
            steps {
                // Set MAVEN_OPTS for this stage to provide more memory for Dependency-Check
                // Adjust -Xmx and -Xms values based on your Jenkins agent's available RAM
                withEnv(['MAVEN_OPTS=-Xmx4G -Xms1G']) {
                    dependencyCheck additionalArguments: ' --scan ./ ', odcInstallation: 'DC'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
        }
        stage('Sonar Analysis') {
            steps {
                // 'sonar-server' is the ID of your SonarQube server in Jenkins config
                withSonarQubeEnv('sonar-server') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \\
                        -Dsonar.projectName=Boardgame-app \\
                        -Dsonar.java.binaries=. \\
                        -Dsonar.projectKey=Boardgame-app
                    '''
                }
            }
        }
        stage('Code-Build') {
            steps {
                sh "mvn clean package"
            }
        }
        stage('Deploy To Nexus') {
            steps {
                // 'settings-file' is the ID of your Maven settings.xml in Jenkins
                withMaven(globalMavenSettingsConfig: 'settings-file', jdk: 'jdk-17', maven: 'maven3', traceability: true) {
                    sh 'mvn deploy'
                }
            }
        }
    }
    // This is the single, global 'post' block that runs after all stages complete
    post {
        always { // This block runs regardless of the pipeline status (success, failure, aborted)
            script {
                // Clean the workspace to free up disk space
                cleanWs() // <--- Added this line for workspace cleanup

                def pipelineStatus = currentBuild.currentResult // Get the final status of the build (SUCCESS, FAILURE, ABORTED, etc.)
                def subject = "Jenkins Pipeline [${env.JOB_NAME}] - Build #${env.BUILD_NUMBER} : ${pipelineStatus}"
                def body = """
                    <p>Hello,</p>
                    <p>Jenkins Pipeline "${env.JOB_NAME}" build #${env.BUILD_NUMBER} has finished with status: <b>${pipelineStatus}</b></p>
                    <p>Check console output at: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    <p>Regards,<br>Jenkins Automation</p>
                """

                // Send email
                // Make sure 'Email Extension Plugin' is installed in Jenkins
                // And Jenkins System Configuration (Manage Jenkins -> Configure System -> Extended E-mail Notification) is set up
                try {
                    mail(
                        to: RECIPIENT_EMAIL,
                        subject: subject,
                        body: body,
                        from: SENDER_EMAIL // Optional: only if you want a specific sender name
                    )
                    echo "Email notification sent to ${RECIPIENT_EMAIL} for build ${pipelineStatus}."
                } catch (e) {
                    echo "Failed to send email notification: ${e.getMessage()}"
                }
            }
        }
    }
}
