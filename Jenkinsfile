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
                sh 'docker run --rm --name cw2 -p 80:80 -d ' + registry + ":$BUILD_NUMBER"
                script {
                    def containerId = sh(script: 'docker ps -q --filter "name=cw2"', returnStatus: true).trim()
                    if (containerId) {
                        sh "docker exec $containerId echo 'I am alive!'"
                        sh "docker stop $containerId"
                    } else {
                        error "Container not running"
                    }		
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
