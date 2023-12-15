pipeline {
    agent any

    environment {
        registry = 'matthewthomasfripp/project:latest'
        registryCredential = 'Docker'
        dockerImage = ''
    }

    stages {
        stage('Build docker image') {
            steps {
                echo 'Building docker image'
                script {
                    dockerImage = docker.build registry
                }
            }
        }

        stage('Test docker container') {
            steps {
                echo 'Testing docker container'
		sh 'docker run --name cw2 -p 80:80 -d ' + registry
		sh 'docker exec cw2 echo "I am alive!"'
		sh 'docker rm -f cw2 || true'
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
        
	stage('Deploy to kubernetes') {
            steps {
                echo 'Deploying to kubernetes'
		sh 'ssh ubuntu@34.224.5.66 kubectl rollout restart deployment/kubernetes-server'
            }
        }
    }
}
