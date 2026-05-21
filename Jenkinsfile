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
            description: 'Skip SonarQube Scan'
        )

        booleanParam(
            name: 'SKIP_COVERAGE',
            defaultValue: false,
            description: 'Skip Code Coverage'
        )
    }

    stages {

        stage('Code Checkout') {

            steps {

                git branch: 'main',
                url: 'https://github.com/Rawatsandeep001/spring-petclinic-ci.git'
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

                        echo 'SonarQube Stage Running'

                        // Uncomment after SonarQube setup

                        /*
                        withSonarQubeEnv('jenkins-test') {

                            sh '''
                            mvn sonar:sonar \
                            -Dsonar.projectKey=jenkins-test
                            '''
                        }
                        */
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

        stage('Generate Report') {

            steps {

                echo 'Generating Build Report'

                junit '**/target/surefire-reports/*.xml'

                archiveArtifacts artifacts: 'target/site/jacoco/*.*', fingerprint: true
            }
        }

        stage('Approval Before Publish') {

            steps {

                input(
                    message: 'Approve Artifact Publish?',
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

            mail(
                to: 'sunraw541@gmail.com',
                subject: "SUCCESS: ${env.JOB_NAME}",
                body: "Build completed successfully"
            )

            // Slack setup required before use

            /*
            slackSend(
                channel: '#jenkins',
                message: "SUCCESS: ${env.JOB_NAME} - Build ${env.BUILD_NUMBER}"
            )
            */
        }

        failure {

            echo 'Build Failed'

            mail(
                to: 'sunraw541@gmail.com',
                subject: "FAILED: ${env.JOB_NAME}",
                body: "Build failed"
            )

            // Slack setup required before use

            /*
            slackSend(
                channel: '#jenkins',
                message: "FAILED: ${env.JOB_NAME} - Build ${env.BUILD_NUMBER}"
            )
            */
        }
    }
}
