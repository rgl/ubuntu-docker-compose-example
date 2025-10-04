pipeline {
    agent {
        label 'ubuntu && docker'
    }
    environment {
        COMPOSE_PROJECT_NAME = env.BUILD_TAG.replaceAll('[^a-zA-Z0-9-]', '-').toLowerCase()
    }
    stages {
        stage('Build') {
            steps {
                sh './test.sh'
            }
        }
    }
}
