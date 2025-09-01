pipeline {
    agent any

    tools {
        maven 'Maven3'   // Define "Maven3" in Jenkins Global Tool Config
        jdk 'JDK11'      // Define "JDK11" in Jenkins Global Tool Config
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
                bat 'mvn clean verify'
            }
        }

        stage('Publish HTML Report') {
            steps {
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'target/jmeter/reports',
                    reportFiles: 'index.html',
                    reportName: 'HRMS JMeter Report'
                ])
            }
        }
    }
}

