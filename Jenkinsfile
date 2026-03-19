pipeline {
    agent any

    environment {
        IMAGE_NAME = "riyaazkhanhub/vote"
        TAG = "v${BUILD_NUMBER}"
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '15'))
        disableConcurrentBuilds()
        retry(2)
        timeout(time: 20, unit: 'MINUTES')
    }

    parameters {
        string(name: 'BRANCH', defaultValue: 'main', description: 'branch to build?')
        choice(name: 'ENV', choices: ['qa', 'dev', 'prod'], description: 'env to build')
    }

    stages {

        stage("Build Docker Image") {
            steps {
                sh '''
                cd vote
                docker build -t $IMAGE_NAME:$TAG .
                '''
            }
        }

        stage("Docker Login & Push") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push $IMAGE_NAME:$TAG
                    '''
                }
            }
        }

        stage("Deploy") {
            steps {
                sh '''
                docker stop vote-container || true
                docker rm vote-container || true
                docker run -d -p 8081:80 --name vote-container $IMAGE_NAME:$TAG
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment Successful 🚀"
        }
        failure {
            echo "Build Failed ❌"
        }
    }
}
