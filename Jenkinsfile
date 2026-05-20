pipeline {
    agent any

    parameters {
        booleanParam(name: 'SKIP_TEST', defaultValue: false, description: 'Skip Unit Test')
        booleanParam(name: 'SKIP_SONAR', defaultValue: false, description: 'Skip SonarQube')
        booleanParam(name: 'SKIP_COVERAGE', defaultValue: false, description: 'Skip Coverage')
    }

    stages {

        stage('Code Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Parallel Scans') {
            parallel {

                stage('Code Stability') {
                    when { expression { !params.SKIP_TEST } }
                    steps {
                        sh 'mvn clean test'
                    }
                }

                stage('Code Quality Analysis') {
                    when { expression { !params.SKIP_SONAR } }
                    steps {
                        withSonarQubeEnv('SonarServer') {
                            sh 'mvn sonar:sonar'
                        }
                    }
                }

                stage('Code Coverage Analysis') {
                    when { expression { !params.SKIP_COVERAGE } }
                    steps {
                        sh 'mvn test jacoco:report'
                    }
                }
            }
        }

        stage('Quality Gate Report') {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }

        stage('Approval Before Publish') {
            steps {
                input message: 'Approve to publish artifact?', ok: 'Approve'
            }
        }

        stage('Publish Artifacts') {
            steps {
                sh 'mvn package'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            slackSend channel: '#jenkins',
                      message: "Build Success: ${env.JOB_NAME}"

            mail to: 'yourmail@gmail.com',
                 subject: 'Build Success',
                 body: 'Build passed successfully'
        }

        failure {
            slackSend channel: '#jenkins',
                      message: "Build Failed: ${env.JOB_NAME}"

            mail to: 'yourmail@gmail.com',
                 subject: 'Build Failed',
                 body: 'Build failed'
        }
    }
}
