pipeline {

```
agent any

stages {

    stage('Code Checkout') {

        steps {
            git 'https://github.com/Rawatsandeep001/spring-petclinic-ci.git'
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
```

}
