pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        // Nexus Config
        NEXUS_URL = 'localhost:8081/repository/maven-releases/' 
        NEXUS_REPO = 'maven-releases'
        NEXUS_CREDENTIALS_ID = 'nexus-admin'

        // Maven Coordinates
        GROUP_ID = 'com.example'
        ARTIFACT_ID = 'springboot-helloworld'
        VERSION = '1.0.0'
        PACKAGING = 'jar'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                sh 'mvn clean test'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Upload to Nexus') {
            steps {
                script {
                    def jarFile = sh(
                        script: "ls target/*.jar",
                        returnStdout: true
                    ).trim()

                    echo "Uploading ${jarFile} to Nexus..."

                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${NEXUS_URL}",
                        groupId: GROUP_ID,
                        version: VERSION,
                        repository: NEXUS_REPO,
                        credentialsId: NEXUS_CREDENTIALS_ID,
                        artifacts: [[
                            artifactId: ARTIFACT_ID,
                            classifier: '',
                            file: jarFile,
                            type: PACKAGING
                        ]]
                    )
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build successful!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
