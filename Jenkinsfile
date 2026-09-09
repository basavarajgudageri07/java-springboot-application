
pipeline {
    agent any

    tools {
        jdk 'java-17'
        maven 'maven'
    }

    environment {
        IMAGE_NAME = "apoorvar12/spring-boot"
        IMAGE_TAG = "$BUILD_NUMBER"
    }

    stages {

        stage('Git Clone') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/basavarajgudageri07/java-springboot-application.git'
            }
        }

        stage('Maven Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🏗️ Building Docker image...'
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerID',
                        passwordVariable: 'DOCKER_PASSWORD',
                        usernameVariable: 'DOCKER_USER'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }

         stage('Update Deployment') {
            steps {
                withCredentials([
                    usernamePassword(
                    credentialsId: 'githubID',
                    usernameVariable: 'GIT_USER',
                    passwordVariable: 'GIT_TOKEN'
                )    
                ]) {
                    sh '''
                        sed -i "s|image: .*|image: ${IMAGE_NAME}:${IMAGE_TAG}|" Deployment.yaml

                        git config user.name "Jenkins"
                        git config user.email "jenkins@example.com"

                        git add Deployment.yaml
                        git commit -m "Update image to ${IMAGE_TAG}" || true

                        git push https://${GIT_USER}:${GIT_TOKEN}@https://github.com/basavarajgudageri07/java-springboot-application.git HEAD:main
                '''    
            }
        }
    }
                
}
}

