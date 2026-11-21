pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out JMeter scripts from GitHub'
                checkout scm
            }
        }

        stage('Verify JMeter') {
            steps {
                bat '''
                "C:\\Users\\LENOVO\\Downloads\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat" -v
                '''
            }
        }

        stage('Execute Performance Test') {
            steps {
                bat '''
                if not exist Results mkdir Results

                "C:\\Users\\LENOVO\\Downloads\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat" -n -t TestScripts\\MACBOOKING.jmx -l Results\\results.jtl -e -o Results\\HTML
                '''
            }
        }

        stage('Publish HTML Report') {
            steps {
                publishHTML(target: [
                    reportDir: 'Results/HTML',
                    reportFiles: 'index.html',
                    reportName: 'JMeter Performance Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: true
                ])
            }
        }
    }
}