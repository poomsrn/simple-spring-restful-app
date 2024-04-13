pipeline {
    agent any

    environment {
        dockerImage = ''
    }

    stages {
        steps {
            script {
                def jdkVersion = '17'
                echo "Using JDK ${jdkVersion}"

                // Build the Maven project
                sh "mvn -B -DskipTests clean package"
                
                // Use the buildPlugin step with the specified configurations
                buildPlugin(
                    useContainerAgent: true,
                    useArtifactCachingProxy: false,
                    configurations: [
                        [platform: 'linux', jdk: jdkVersion],
                        [platform: 'windows', jdk: jdkVersion]
                    ]
                )
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