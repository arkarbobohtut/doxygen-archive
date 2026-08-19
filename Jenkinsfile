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
                    sed -i 's|^INPUT *=.*|INPUT = src|' Doxyfile
                    sed -i 's|^OUTPUT_DIRECTORY *=.*|OUTPUT_DIRECTORY = docs|' Doxyfile
                    sed -i 's|^RECURSIVE *=.*|RECURSIVE = YES|' Doxyfile
                    sed -i 's|^GENERATE_LATEX *=.*|GENERATE_LATEX = NO|' Doxyfile
                    sed -i 's|^WARN_LOGFILE *=.*|WARN_LOGFILE = warnings.log|' Doxyfile

                    echo "===== Doxyfile Configuration ====="
                    grep -E '^INPUT *=|^OUTPUT_DIRECTORY *=|^RECURSIVE *=|^GENERATE_LATEX *=|^WARN_LOGFILE *=' Doxyfile
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

        stage('Checkout Repo C') {
            steps {
                dir('parser-repo') {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '*/main']],
                        userRemoteConfigs: [[
                            url: 'https://github.com/arkarbobohtut/doxygen-log-parse-with-python.git'
                        ]],
                        extensions: [
                            [$class: 'CloneOption',
                             shallow: true,
                             depth: 1,
                             noTags: true,
                             timeout: 30]
                        ]
                    ])
                }
            }
        }

        stage('Run Parser') {
            steps {
                dir('parser-repo') {
                    sh '''
                        python3 parser.py ../warnings.log warnings.csv
                    '''
                }
            }
        }

    }
}