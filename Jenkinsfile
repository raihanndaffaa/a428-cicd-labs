node {
    def image = 'node:16-buster-slim'
    def container

    try {
        container = docker.image(image).withRun('-p 3000:3000') { c ->
            stage('Build') {
                sh '''
                    apt-get update &&
                    apt-get install -y nodejs npm &&
                    npm install
                '''
            }

            stage('Test') {
                sh './jenkins/scripts/test.sh'
            }
        }
    } finally {
        if (container != null) {
            container.stop()
            container.remove()
        }
    }
}