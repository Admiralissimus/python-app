def dockerRun = "docker run -d -p 8081:5000 ambarodzich/docker-app:$BUILD_NUMBER"

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
                sh 'docker build -t ambarodzich/docker-app:$BUILD_NUMBER .'   
            }
        }
        stage('Docker_Push') {
            steps {
                withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
                    sh "docker login -u ambarodzich -p $DockerHubPwd"
                }
                sh 'docker push ambarodzich/docker-app:$BUILD_NUMBER'   
            }
        }
        stage('Deploy') {
            steps {
                sshagent(['devserver']) {
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@3.89.27.36 ${dockerRun}" 
                }
            }
        }
    }
}
