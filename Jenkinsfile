pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/RevathiC31/HelloWorld.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-login') {
                        def app = docker.build("revathic31/hello-flask:latest")
                        app.push()
                    }
                }
            }
        }

        stage('Run Container Test') {
            steps {
                sh 'docker run -d -p 5000:5000 --name test-flask revathic31/hello-flask:latest'
                sh 'sleep 5'
                sh 'curl http://localhost:5000'
                sh 'docker rm -f test-flask'
            }
        }
    }
}
