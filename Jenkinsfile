pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/RevathiC31/HelloWorld.git'
            }
        }

        stage('Build Docker Image') {          // Define a pipeline stage named "Build Docker Image"
            steps {                            // Inside this stage, specify the steps to execute
                script {                       // Use a script block to run Groovy code (needed for docker pipeline methods)
                    docker.withRegistry(       // Authenticate and connect to a Docker registry
                        'https://index.docker.io/v1/', // The DockerHub registry URL
                        'dockerhub-login'      // Jenkins credentials ID for DockerHub login
                    ) {
                        def app = docker.build(        // Build a Docker image from the Dockerfile in the workspace
                            "revathic31/hello-flask:latest" // Name and tag of the image (DockerHub username/repo:tag)
                        )
                        app.push()             // Push the built image to DockerHub under the specified repository
                    }
                }
            }
        }

        stage('Run Container Test') {
            steps {
                // cleanup if container exists
                bat 'docker rm -f test-flask || echo "No existing container"'
        
                // run new container
                bat 'docker run -d -p 5000:5000 --name test-flask revathic31/hello-flask:latest'
        
                // wait for startup
                sleep time: 5, unit: 'SECONDS'
        
                // test endpoint
                bat 'powershell -Command "(Invoke-WebRequest http://localhost:5000).Content"'
        
                // cleanup
                bat 'docker rm -f test-flask'
            }
        }
    }
}
