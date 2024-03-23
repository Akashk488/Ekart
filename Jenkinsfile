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

            stage('OWASP Dependency check'){
                steps{
                    dependencyCheck additionalArguments: ' --scan ./', odcInstallation: 'Dc' //--scan ./ check depency of pom file
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }

            }

            stage('Build'){
                steps{
                    sh 'mvn package -DskipTests=true' //build a artificate but wont test
                }

            }

            stage('Deploy to Nexus'){
                steps{
                    withMaven(globalMavenSettingsConfig: 'global-maven', jdk: 'jdk17', maven: 'maven', mavenSettingsConfig: '', traceability: true) {
                    sh 'mvn deploy -DskipTests=true'
                    }
                }

            }
            
        }
}
