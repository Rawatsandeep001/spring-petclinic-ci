pipeline {

    agent any

    stages {

        stage('Code Checkout') {

            steps {
                checkout scm
            }
        }

        stage('Parallel Scans') {

            parallel {

                stage('Code Stability') {

                    steps {
                        sh 'mvn clean test'
                    }
                }

                stage('Code Quality Analysis') {

                    steps {
                        echo 'SonarQube analysis stage'
                    }
                }

                stage('Code Coverage Analysis') {

                    steps {
                        sh 'mvn test jacoco:report'
                    }
                }
            }
        }
    }
}
