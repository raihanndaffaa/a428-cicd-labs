node {
    def dockerImage = 'node:16-buster-slim'
    
    try {
        // Stage: Build
        stage('Build') {
            echo 'Building the project...'
            docker.image(dockerImage).inside("-p 3000:3000") {
                sh 'npm install'
            }
        }
        
        // Stage: Test
        stage('Test') {
            echo 'Running tests...'
            docker.image(dockerImage).inside("-p 3000:3000") {
                sh './jenkins/scripts/test.sh'
            }
        }

        // Stage: Manual Approval
        stage('Manual Approval') {
            options {
                timeout(time: 1, unit: 'MINUTES')
            }
            steps {
                script {
                    def userInput = input message: 'Lanjutkan ke tahap Deploy?', parameters: [
                        choice(name: 'ACTION', choices: 'Proceed\nAbort', description: 'Pilih tindakan')
                    ]
                    if (userInput == 'Proceed') {
                        echo 'Proceed to Deploy...'
                    } else {
                        error 'Pipeline aborted by user.'
                    }
                }
            }
        }
        
        // Stage: Deploy
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