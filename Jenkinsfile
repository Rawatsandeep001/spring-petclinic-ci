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
                    mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=jenkins-test \
                    -Dsonar.host.url=http://192.168.73.191:9000
                    '''
                }
            }
        }

        stage('Code Coverage Analysis') {

            when {
                expression { !params.SKIP_COVERAGE }
            }

            steps {

                sh 'mvn jacoco:report'
            }
        }

        stage('Quality Gate') {

            when {
                expression { !params.SKIP_SONAR }
            }

            steps {

                timeout(time: 2, unit: 'MINUTES') {

                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Generate Report') {

            steps {

                echo 'Generating Build Report'

                junit '**/target/surefire-reports/*.xml'

                archiveArtifacts(
                    artifacts: 'target/site/jacoco/**/*',
                    fingerprint: true
                )
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

                sh 'mvn package -DskipTests'

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
        }

        failure {

            echo 'Build Failed'

            mail(
                to: 'sunraw541@gmail.com',
                subject: "FAILED: ${env.JOB_NAME}",
                body: "Build failed"
            )
        }
    }
}
