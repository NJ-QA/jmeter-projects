pipeline {
    agent any

    environment {
          DOCKER_IMAGE = 'justb4/jmeter:5.6.2'     // Docker image for JMeter
    }

    stages {
        stage('Checkout Git Repo') {
            steps {
                git url: 'https://github.com/NJ-QA/jmeter-projects.git', branch: 'main', credentialsId: '8253c755-3429-4db7-bac1-a800e7e9edcd'
            }
        }

        stage('Verify CSVs') {
            steps {
                script {
                    // List files in CSV folder
                    bat 'dir "data-files\\CSVs"'

                    // Verify specific CSV exists
                    bat """
                    if exist "data-files\\CSVs\\leave_application.csv" (
                        echo CSV file exists.
                        type "data-files\\CSVs\\leave_application.csv"
                    ) else (
                        echo CSV file NOT FOUND!
                        exit /b 1
                    )
                    """
                }
            }
        }

        stage('Run JMeter in Docker') {
            steps {
                script {
                    // Mount workspace and run JMeter in Docker
                    bat """
                    docker run --rm -v "%cd%:/jmeter" -w /jmeter ${DOCKER_IMAGE} \
                        -n -t target\\jmeter\\testFiles\\HRMS_MB.jmx \
                        -l target\\jmeter\\results\\HRMS_MB.csv \
                        -j target\\jmeter\\logs\\HRMS_MB.log \
                        -f
                    """
                }
            }
        }

        stage('Generate HTML Report') {
            steps {
                script {
                    bat """
                    docker run --rm -v "%cd%:/jmeter" -w /jmeter ${DOCKER_IMAGE} \
                        -g target\\jmeter\\results\\HRMS_MB.csv \
                        -o target\\jmeter\\reports\\HRMS_MB
                    """
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'target\\jmeter\\reports\\HRMS_MB\\**\\*', allowEmptyArchive: true
            echo "JMeter test finished. Check reports and logs."
        }
        failure {
            echo "JMeter Docker test failed. Check CSV path, JMX, or Docker configuration."
        }
    }
}
