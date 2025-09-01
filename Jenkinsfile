pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK11'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/NJ-QA/jmeter-projects.git'
            }
        }

        stage('Run JMeter Tests') {
            steps {
                withEnv(["PATH+MAVEN=${tool 'Maven3'}/bin"]) {
                    sh 'mvn clean verify'
                }
            }
        }

        stage('Publish JMeter HTML Report') {
            steps {
                script {
                    // Find the first report folder inside target/jmeter/reports
                    def reportDir = sh(
                        script: "ls target/jmeter/reports | head -n 1",
                        returnStdout: true
                    ).trim()

                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: "target/jmeter/reports/${reportDir}",
                        reportFiles: 'index.html',
                        reportName: 'JMeter Test Report'
                    ])
                }
            }
        }
    }
}
