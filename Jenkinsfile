pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('Upload to Nexus') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: 'localhost:8081',
                    groupId: 'com.example',
                    version: '1.0.0',
                    repository: 'maven-releases',
                    credentialsId: 'nexus-admin',
                    artifacts: [
                        [
                            artifactId: 'gs-spring-boot',
                            classifier: '',
                            file: 'target/gs-spring-boot-helloworld-0.1.0.jar',
                            type: 'jar'
                        ]
                    ]
                )
            }
        }
    }

    post {
        success {
            echo "Build & Upload Successful!"
        }
        failure {
            echo "Build Failed!"
        }
    }
}
