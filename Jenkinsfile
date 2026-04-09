pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'readtext/app'
        DOCKER_TAG = 'latest'
        CONTAINER_NAME = 'readtext-container'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/naresh041/readtext.git'
            }
        }

        stage('Build JAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$DOCKER_TAG .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $DOCKER_IMAGE:$DOCKER_TAG
                    '''
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                  # Remove old container (safe)
                docker rm -f $CONTAINER_NAME || true
                # Run new container
                docker run -d -p 8081:8080 --name $CONTAINER_NAME $DOCKER_IMAGE:$DOCKER_TAG
                '''
            }
        }
    }
}
