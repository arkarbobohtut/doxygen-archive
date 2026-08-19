pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/rkarbobohtut/casino-number-guessing-game.git'
            }
        }
    agent {
        docker { 
            // Pulls an image that already has doxygen pre-installed
            image 'alpine/doxygen:latest' 
        }
    }
    stages {
        stage('Generate Docs') {
            steps {
                // Run doxygen directly inside the container
                sh 'doxygen Doxyfile'
            }
        }
    }

        // stage('Generate Doxyfile') {
        //     steps {
        //         sh '''
        //             doxygen -g Doxyfile
        //         '''
        //     }
        // }

        // stage('Configure Doxyfile') {
        //     steps {
        //         sh '''
        //             sed -i 's|^INPUT *=|INPUT = src|' Doxyfile
        //             sed -i 's|^OUTPUT_DIRECTORY.*|OUTPUT_DIRECTORY = docs|' Doxyfile
        //             sed -i 's|^RECURSIVE.*|RECURSIVE = YES|' Doxyfile
        //             sed -i 's|^GENERATE_LATEX.*|GENERATE_LATEX = NO|' Doxyfile
        //         '''
        //     }
        // }

        // stage('Run Doxygen') {
        //     steps {
        //         sh '''
        //             doxygen Doxyfile
        //         '''
        //     }
        // }

        // stage('Create Archive') {
        //     steps {
        //         sh '''
        //             tar -czf doc.tar.gz -C docs html
        //         '''
        //     }
        // }

        // stage('Archive Artifact') {
        //     steps {
        //         archiveArtifacts artifacts: 'doc.tar.gz',
        //                          fingerprint: true
        //     }
        // }
    }
}