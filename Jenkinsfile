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

            stage('Build docker image'){
                steps{
                    script {
                        withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh 'docker build -t akash488/ekart:latest -f docker/Dockerfile .'
                        }
                    }
                }

            }

            stage('Trivy Scan'){
                steps{
                    sh 'trivy image akash488/ekart:latest > trivy-report.txt'
                }

            }

            stage('Build docker image push'){
                steps{
                    script {
                        withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh 'docker push akash488/ekart:latest'
                        }
                    }
                }

            }

            stage('Kubernate deploy'){
                steps{
                    withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.82.58:6443') {
                         sh 'kubectl apply -f deploymentservice.yaml -n webapps'
                         sh 'kubectl get svc -n webapps'   
                        }
                }

            }
            
        }
}
