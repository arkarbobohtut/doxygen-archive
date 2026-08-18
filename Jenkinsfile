pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: ''
            }
        }

        stage('Check Source') {
            steps {
                sh '''
                    echo "Workspace:"
                    pwd

                    echo ""
                    echo "Repository files:"
                    ls -la

                    echo ""
                    echo "Git information:"
                    git status
                    git log -1 --oneline
                '''
            }
        }
    }
}