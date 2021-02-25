pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "M3"
    }

    stages {
        stage('Build') {
            steps {
                git 'https://github.com/garymsegal/WebGoat.git'

                sh "mvn clean install"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit '**/webgoat-integration-tests/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'webgoat-server/target/*.jar'
                }
            }
        }
        
        stage('Dependency Check') {
            steps {
                sh 'mkdir -p dc-reports'
                dependencyCheck additionalArguments: '''--project WebGoat --format XML --format HTML -o dc-reports --scan .''', odcInstallation: 'Dependency-Check'
            }
            post {
                success {
                     dependencyCheckPublisher pattern: 'dc-reports/dependency-check-report.xml'
                     archiveArtifacts 'dc-reports/dependency-check-report.html'
                     publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'dc-reports', reportFiles: 'dc-reports/dependency-check-report.html', reportName: 'Dependency Check Report', reportTitles: ''])
                }
            }
        }
    }
}

