pipeline {
    agent any

    environment {
        dockerImage = ''
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn -U -DskipTests clean package'
            }
        }
        stage('Build image') {
            steps {
                script {
                    dockerImage = docker.build("poomsrn/simple-spring-restful-app")
                }
            }
        }
        stage('Push image') {
            steps {
                script {
                    withDockerRegistry(
                        credentialsId: 'docker-credential',
                        url: 'https://index.docker.io/v1/') {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Deployment') {
            steps {
                sh 'kubectl apply -f myapp-deployment.yml';
            }
        }
    }
}