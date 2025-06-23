pipeline {
    agent any

    environment {
        registryUrl = 'docker.io'
        registryCredential = 'docker-login'  // ID configured in Jenkins Credentials
        dockerImageName = manojsavukar/sonali'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/spring-projects/spring-petclinic.git'
            }
        }

        stage('Build') {
            steps {
                sh './mvnw clean package'
            }
        }

        stage('Create Dockerfile') {
    steps {
        writeFile file: 'Dockerfile', text: '''
FROM eclipse-temurin:17-jdk
WORKDIR /app
COPY target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
'''
    }
}

                stage('Docker Build') {
            steps {
                sh 'docker build -t sonalikurade/devops:latest .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push sonalikurade/devops:latest'
                }
            }
        }


        stage ('K8S Deploy') {
          steps {
            script {
                withKubeConfig([credentialsId: 'k8s']) {
                sh ('kubectl apply -f  ./k8s/deployment.yaml')
                }
            }
        }
     }
    }
}
