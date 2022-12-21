node {
    stage('Checkout'){
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AMBarodzich/pythonapp']]])
    }
    stage('Docker_build'){
        sh 'docker build -t prodpython .'
    }
    stage('Docker_run'){
        sh 'docker run -d -p 8082:5000 prodpython'
    }
}
