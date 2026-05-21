pipeline {

agent any

stages {

    stage('Parallel Scans') {

        parallel {

            stage('Code Stability') {

                steps {
                    sh 'mvn clean test'
                }
            }

            stage('Code Quality Analysis') {

                steps {
                    echo 'SonarQube Stage Running'
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
