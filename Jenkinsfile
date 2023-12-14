pipeline {
    agent any

    environment {
        registry = 'matthewthomasfripp/project'
        registryCredential = 'Docker'
        dockerImage = ''
    }

    stages {
        stage('Build docker image') {
            steps {
                echo 'Building docker image'
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }

        stage('Test docker container') {
            steps {
                echo 'Testing docker container'
                
                // Remove the container if it exists
                sh 'docker rm -f cw2 || true'
                
                sh 'docker run --rm --name cw2 -p 80:80 -d ' + registry + ":$BUILD_NUMBER"
                
                def containerId = sh(script: 'docker ps -q --filter "name=cw2"', returnStatus: true).trim()
                if (containerId) {
                    sh "docker exec $containerId echo 'I am alive!'"
                } else {
                    error "Container not running"
                }
                
                sh 'docker rm -f cw2'
            }
        }

        stage('Push docker image') {
            steps {
                echo 'Pushing docker image'
                script {
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }
    }
}
