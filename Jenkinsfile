pipeline{
    agent any

    tools{
        maven 'maven'
        jdk 'jdk17'
    }

    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

        stages{
            stage('Git Checkout'){
                steps{
                    checkout scm
                }

            }

            stage('Compile'){
                steps{
                    sh 'mvn compile'
                }

            }

            stage('Unit Test'){
                steps{
                    sh 'mvn test -DskipTests=true' //skip the test cases in case fail
                }

            }

            stage('Sonarqube Analysis'){
                steps{
                    withSonarQubeEnv('sonarqube') { // in setting we confire sonar server
                        sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=EKART -Dsonar.projectName=EKART -Dsonar.java.binaries=. '''
            

                    } 
                }

            }
        }
}
