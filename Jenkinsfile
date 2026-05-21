pipeline {

    agent any

    parameters {

        booleanParam(
            name: 'SKIP_TEST',
            defaultValue: false,
            description: 'Skip Unit Test'
        )

        booleanParam(
            name: 'SKIP_SONAR',
            defaultValue: false,
            description: 'Skip SonarQube'
        )

        booleanParam(
            name: 'SKIP_COVERAGE',
            defaultValue: false,
            description: 'Skip Coverage'
        )
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

                    when {
                        expression { !params.SKIP_TEST }
                    }

                    steps {

                        sh 'mvn clean test'
                    }
                }

                stage('Code Quality Analysis') {

                    when {
                        expression { !params.SKIP_SONAR }
                    }

                    steps {

                        withSonarQubeEnv('jenkins-test') {

                            sh '''
                            mvn sonar:sonar \
                            -Dsonar.projectKey=jenkins-test
                            '''
                        }
                    }
                }

                stage('Code Coverage Analysis') {

                    when {
                        expression { !params.SKIP_COVERAGE }
                    }

                    steps {

                        sh 'mvn test jacoco:report'
                    }
                }
            }
        }

        stage('Quality Gate Report') {

            when {
                expression { !params.SKIP_SONAR }
            }

            steps {

                timeout(time: 2, unit: 'MINUTES') {

                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Approval Before Publish') {

            steps {

                input(
                    message: 'Approve to publish artifact?',
                    ok: 'Approve'
                )
            }
        }

        stage('Publish Artifacts') {

            steps {

                sh 'mvn package'

                archiveArtifacts(
                    artifacts: 'target/*.jar',
                    fingerprint: true
                )
            }
        }
    }

    post {

        success {

            echo 'Build Success'

            // Slack Notification
            slackSend(
                channel: '#jenkins',
                message: "SUCCESS: ${env.JOB_NAME} - Build ${env.BUILD_NUMBER}"
            )

            // Email Notification
            mail(
                to: 'sunraw541@gmail.com',
                subject: "SUCCESS: ${env.JOB_NAME}",
                body: "Build completed successfully"
            )
        }

        failure {

            echo 'Build Failed'

            // Slack Notification
            slackSend(
                channel: '#jenkins',
                message: "FAILED: ${env.JOB_NAME} - Build ${env.BUILD_NUMBER}"
            )

            // Email Notification
            mail(
                to: 'sunraw541@gmail.com',
                subject: "FAILED: ${env.JOB_NAME}",
                body: "Build failed"
            )
        }
    }
}
