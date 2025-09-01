pipeline {
    agent any

    environment {
        JMETER_HOME = "${WORKSPACE}/jmeter-docker"
        CSV_PATH = "data-files/CSVs/leave_application.csv"
        JMX_FILE = "testFiles/HRMS_MB.jmx"
        REPORT_DIR = "target/jmeter/reports/HRMS_MB"
    }

    stages {
        stage('Checkout Git Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/NJ-QA/jmeter-projects.git'
            }
        }

        stage('Verify CSV') {
            steps {
                script {
                    // List files for debug
                    bat "dir data-files/CSVs"

                    // Check CSV exists
                    bat """
                    if exist "${CSV_PATH}" (
                        echo CSV file exists.
                        type "${CSV_PATH}"
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
                    // Pull JMeter Docker image
                    bat "docker pull justb4/jmeter:5.6.2"

                    // Run JMeter container
                    bat """
                    docker run --rm ^
                        -v "${WORKSPACE}:/jmeter" ^
                        -w /jmeter ^
                        justb4/jmeter:5.6.2 ^
                        -n -t ${JMX_FILE} -l target/jmeter/results/result.jtl -j target/jmeter/logs/jmeter.log -e -o ${REPORT_DIR}
                    """
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'target/jmeter/reports/**', allowEmptyArchive: true
            archiveArtifacts artifacts: 'target/jmeter/results/**', allowEmptyArchive: true
            archiveArtifacts artifacts: 'target/jmeter/logs/**', allowEmptyArchive: true
        }
        success {
            echo 'JMeter Docker test completed successfully.'
        }
        failure {
            echo 'JMeter Docker test failed. Check CSV path, JMX, or Docker configuration.'
        }
    }
}
