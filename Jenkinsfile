pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AMBarodzich/pythonapp']])
            }
        }
        stage('Docker_Build') {
            steps {
               sh 'docker build -t \'ambarodzich/docker-app:v1\' .' 
            }
        }
        stage('Docker_Push') {
            steps {
                withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
                    sh "docker login -u ambarodzich -p $DockerHubPwd"
                }
                sh 'docker push \'ambarodzich/docker-app:v1\'' 
            }
        }
        stage('Deploy') {
            steps {
               sh 'docker run -p 8081:5000 -d \'ambarodzich/docker-app:v1\'' 
            }
        }
    }
}
