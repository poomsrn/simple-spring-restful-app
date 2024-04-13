pipeline {
    buildPlugin(
        useContainerAgent: true, // Set to `false` if you need to use Docker for containerized tests
        configurations: [
            [platform: 'linux', jdk: 17],
            [platform: 'windows', jdk: 11],
    ])

    environment {
        dockerImage = ''
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
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