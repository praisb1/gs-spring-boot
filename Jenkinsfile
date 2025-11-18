pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
    }

    tools {
        maven 'Maven3'
    }
environment {
    NEXUS_URL = 'localhost:8081'
    NEXUS_REPO = 'maven-releases'
    NEXUS_CREDENTIALS = 'nexus-admin'
}


    stages {
        stage('Checkout Source Code') {
            steps {
                git branch: 'main', url: 'https://github.com/praisb1/gs-spring-boot.git'
            }
        }

        stage('Test') {
            steps {
                sh 'git --version'
                sh 'mvn --version'
                sh 'mvn clean test' // Example for a Maven project
            }
        }

        stage('Build and Package') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }
stage('Upload to Nexus') {
    steps {
        script {
            def jarFile = sh(
                script: "ls target/*.jar",
                returnStdout: true
            ).trim()

            nexusArtifactUploader(
                nexusVersion: 'nexus3',
                protocol: 'http',
                nexusUrl: "${NEXUS_URL}",
                groupId: "${GROUP_ID}",
                version: "${VERSION}",
                repository: "${NEXUS_REPO}",
                credentialsId: "${NEXUS_CREDENTIALS_ID}",
                artifacts: [[
                    artifactId: "${ARTIFACT_ID}",
                    classifier: '',
                    file: jarFile,
                    type: "${PACKAGING}"
                ]]
            )
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
