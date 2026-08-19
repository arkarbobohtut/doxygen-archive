pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/master']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/arkarbobohtut/grpc.git'
                    ]],
                    extensions: [
                        [$class: 'CloneOption',
                         shallow: false,
                         noTags: true,
                         timeout: 60]
                    ]
                ])
            }
        }

        stage('Generate Doxyfile') {
            steps {
                sh '''
                    doxygen -g Doxyfile
                '''
            }
        }

        stage('Configure Doxyfile') {
            steps {
                sh '''
                    sed -i 's|^INPUT *=.*|INPUT = grpc/src|' Doxyfile
                    sed -i 's|^OUTPUT_DIRECTORY *=.*|OUTPUT_DIRECTORY = docs|' Doxyfile
                    sed -i 's|^RECURSIVE *=.*|RECURSIVE = YES|' Doxyfile
                    sed -i 's|^GENERATE_LATEX *=.*|GENERATE_LATEX = NO|' Doxyfile
                    grep -e "^INPUT *=|^OUTPUT_DIRECTORY *=|^RECURSIVE *=|^GENERATE_LATEX *=" Doxyfile
                '''
            }
        }

        stage('Run Doxygen') {
            steps {
                sh '''
                    doxygen Doxyfile
                '''
            }
        }

        stage('Create Archive') {
            steps {
                sh '''
                    tar -czf doc.tar.gz -C docs html
                '''
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'doc.tar.gz',
                                 fingerprint: true
            }
        }
    }
}