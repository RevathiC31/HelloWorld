pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
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

        stage('Run Container Test') {          // Define a pipeline stage named "Run Container Test"
            steps {                            // Inside this stage, specify the steps Jenkins should execute
                bat 'docker rm -f test-flask || echo "No existing container"' 
                // 👉 Runs a Windows batch command.
                // Tries to remove any existing container named "test-flask".
                // If it doesn’t exist, prints "No existing container" instead of failing.
        
                bat 'docker run -d -p 5000:5000 --name test-flask revathic31/hello-flask:latest'
                // 👉 Starts a new Docker container in detached mode (-d).
                // Maps port 5000 on the host to port 5000 in the container.
                // Names the container "test-flask".
                // Uses the image "revathic31/hello-flask:latest" from DockerHub.
        
                sleep time: 15, unit: 'SECONDS'
                // 👉 Jenkins built‑in sleep step.
                // Waits 15 seconds to give the container time to start up before testing.
        
                bat 'docker logs test-flask'
                // 👉 Shows the logs from the running container.
                // Useful for debugging if the Flask app failed to start or crashed.
        
                bat 'powershell -Command "$resp = Invoke-WebRequest http://127.0.0.1:5000 -UseBasicParsing; if ($resp) { $resp.Content } else { Write-Output \'No response\' }"'
                // 👉 Runs a PowerShell command inside Jenkins.
                // Sends an HTTP GET request to http://127.0.0.1:5000.
                // If a response is received, prints the response body (.Content).
                // If no response, prints "No response".
        
                bat 'docker rm -f test-flask'
                // 👉 Cleans up by force‑removing the "test-flask" container after the test.
                // Prevents conflicts in future builds.
            }
        }
    }
}
