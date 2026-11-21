pipeline {
    agent any

    options {
        timestamps()
    }

    parameters {
        string(
            name: 'USERS',
            defaultValue: '5',
            description: 'Number of virtual users'
        )

        string(
            name: 'RAMPUP',
            defaultValue: '10',
            description: 'Ramp-up time in seconds'
        )

        string(
            name: 'DURATION',
            defaultValue: '300',
            description: 'Test duration in seconds'
        )

        choice(
            name: 'ENVIRONMENT',
            choices: ['QA', 'DEV', 'UAT'],
            description: 'Target environment'
        )

        string(
            name: 'BASE_URL',
            defaultValue: 'api.restful-api.dev',
            description: 'API host without https://'
        )
    }

    environment {
        JMETER_HOME = 'C:/Users/LENOVO/Downloads/apache-jmeter-5.6.3/apache-jmeter-5.6.3'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
                echo 'Source code checked out successfully'
            }
        }

        stage('Verify JMeter') {
            steps {
                bat '"%JMETER_HOME%/bin/jmeter.bat" -v'
            }
        }

        stage('Execute Performance Test') {
            steps {
                bat '''
                    if not exist Results mkdir Results

                    if exist Results\\results.jtl (
                        del /f /q Results\\results.jtl
                    )

                    if exist Results\\HTML (
                        rmdir /s /q Results\\HTML
                    )

                    "%JMETER_HOME%\\bin\\jmeter.bat" ^
                    -n ^
                    -t "TestScripts\\MACBOOKING.jmx" ^
                    -l "Results\\results.jtl" ^
                    -e ^
                    -o "Results\\HTML" ^
                    -Jusers=%USERS% ^
                    -Jrampup=%RAMPUP% ^
                    -Jduration=%DURATION% ^
                    -Jhost=%BASE_URL% ^
                    -Jenvironment=%ENVIRONMENT%

                    if errorlevel 1 exit /b 1
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

    post {
        always {
            archiveArtifacts(
                artifacts: 'Results/**',
                allowEmptyArchive: true
            )
        }
    }
}