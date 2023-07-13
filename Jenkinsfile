def webServerIP = "10.128.0.33"

pipeline {
    agent any

    stages {
        stage('CheckoutSCM') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Admiralissimus/python-app.git']])
            }
        }
        stage('DockerBuild') {
            steps {
                sh 'docker build -t "admiralissimus/docker-web-python:v$BUILD_NUMBER" -t "admiralissimus/docker-web-python:latest" .'
            }
        }
        stage('DockerPush') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DockerHubToken', passwordVariable: 'DockerHubPwd', usernameVariable: 'DockerHubUser')]) {
                    sh 'docker login -u "$DockerHubUser" -p "$DockerHubPwd" '
                }
                sh 'docker push "admiralissimus/docker-web-python:v$BUILD_NUMBER"'
                sh 'docker push "admiralissimus/docker-web-python:latest"'
            }
        }
        stage('Deploy') {
            steps {
                sshagent(['test_2']) {
                    sh '''
                        [ -d ~/.ssh ] || mkdir ~/.ssh && chmod 0700 ~/.ssh
                        ssh-keyscan -t rsa,dsa ${webServerIP} >> ~/.ssh/known_hosts
                        ssh ubuntu@${webServerIP} docker rm -f python-web || true
                        ssh ubuntu@${webServerIP} docker pull admiralissimus/docker-web-python:latest
                        ssh ubuntu@${webServerIP} docker run --name python-web -p 8080:5000 -d admiralissimus/docker-web-python:latest
                    '''
                }
            }
        }
    }
}
