pipeline {
    agent any

    stages {
        stage('SCM checkout') {
            steps {
                git 'https://github.com/Sokolov0993/JenkinsCI'
            }
        }
        
        stage('Compile-Package') {
            environment {
                mvnHome = '/opt/homebrew/Cellar/maven/3.9.1/libexec'
            }
            steps {
                sh "${env.mvnHome}/bin/mvn package"
            }
        }
        
        stage('Test') {
            steps {
                sh 'echo "Running tests"'
                sh "${env.mvnHome}/bin/mvn test"
            }
        }
        
        stage('Slack notification') {
            steps {
                script {
                    def testReport = readFile 'target/surefire-reports/TEST-TestSuite.xml'
                    def testSuite = testReport.testsuite[0]
                    
                    def totalTests = testSuite.@tests
                    def passedTests = testSuite.@tests - testSuite.@failures - testSuite.@skipped
                    def failedTests = testSuite.@failures
                    def skippedTests = testSuite.@skipped
                    
                    def slackMessage = "*Test Report:*"
                    slackMessage += "\nTotal Tests: ${totalTests}"
                    slackMessage += "\nPassed Tests: ${passedTests}"
                    slackMessage += "\nFailed Tests: ${failedTests}"
                    slackMessage += "\nSkipped Tests: ${skippedTests}"
                    slackMessage += "\n\nBuild details: [${env.JOB_NAME} #${env.BUILD_NUMBER}](${env.BUILD_URL})"
                    
                    slackSend(channel: 'U04P5KR3H8S', message: slackMessage)
                }
            }
        }
    }
}

