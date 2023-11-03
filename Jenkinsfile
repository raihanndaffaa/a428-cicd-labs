node {
    def dockerImage = 'node:16-buster-slim'

    try {
        // Tahapan: Build
        stage('Build') {
            echo 'Building the project...'
            docker.image(dockerImage).inside("-p 3000:3000") {
                sh 'npm install'
            }
        }

        // Tahapan: Test
        stage('Test') {
            echo 'Running tests...'
            docker.image(dockerImage).inside("-p 3000:3000") {
                sh './jenkins/scripts/test.sh'
            }
        }

        // Tahapan: Manual Approval
        stage('Manual Approval') {
            input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed', parameters: [choice(choices: ['Proceed', 'Abort'], description: 'Pilih tindakan', name: 'ACTION')]
        }

        // Tahapan: Deploy
        stage('Deploy') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                sleep 60 // Menjeda eksekusi selama 1 menit
                sh './jenkins/scripts/kill.sh'
            }
        }
    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        error "Build, test, or deploy failed: ${e.message}"
    }
}