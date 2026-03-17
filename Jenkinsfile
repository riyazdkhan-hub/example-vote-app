pipeline {
    agent { label 'worker' }

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

        stage("Docker login and push") {
            steps {

                sh "docker login -u riyazkhanhub -p p@ssW0rd#123"

                sh '''
                cd vote
                docker build -t riyazkhanhub/vote:v${BUILD_NUMBER} .
                '''

                sh "docker push riyazkhanhub/vote:v${BUILD_NUMBER}"

            }
        }

        stage("deploy") {
            steps {
                sh "echo starting deployment"
            }
        }

    }
}
