properties([pipelineTriggers([githubPush()])])

pipeline {
    agent any
    triggers {
        pollSCM('')
    }
    tools { 
        maven 'Maven 3.6.1' 
        jdk 'JDK 8u202' 
    }
    environment {
        ARTIFACT = readMavenPom().getArtifactId()
        VERSION = readMavenPom().getVersion()
    }
    stages {
        stage('Maven Build') {
            steps {
                echo 'Building with Maven'
                sh 'mvn package'    
            }
        }
        stage('SonarQube Analysis') {
            environment {
                sonarScanner = tool name: 'SonarQube Scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
            }
            steps {
                withSonarQubeEnv('Sonarqube Di2e Server') {
                    echo 'Running SonarQube Analysis'
                    sh '${sonarScanner}/bin/sonar-scanner'
                }
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }

            }
            post {
                failure {
                    emailext(
                        subject: "SonarQubed quality gate FAILED Build: ${env.BUILD_ID}",
                        body: "Job Name: ${env.JOB_NAME} \nBuild Number: ${env.BUILD_NUMBER}",
                        to:"william.mars@di2e.net"
                    )
                }
                success {
                    emailext(
                        subject: "SonarQubed quality gate PASSED Build: ${env.BUILD_ID}",
                        body: "Job Name: ${env.JOB_NAME} \nBuild Number: ${env.BUILD_NUMBER}",
                        to:"william.mars@di2e.net"
                    )
                }
            }
        }
        stage('Nexus Upload') {
            steps {
                    echo 'Deploying to Nexus'
                    nexusArtifactUploader (
                        nexusVersion: 'nexus3',
                        protocol: 'https',
                        nexusUrl: 'nexus.di2e.net/nexus3',
                        groupId: 'org.jenkins-ci.plugins',
                        version: "${env.VERSION}",
                        repository: 'public_DI2E_maven',
                        credentialsId: '687110ca-29bc-48c6-b35a-50b6040f1260',
                        artifacts: [
                            [artifactId: "${env.ARTIFACT}",
                            type: 'jar',
                            classifier: 'debug',
                            file: 'target/keycloak.jar']
                        ]
                    )
            }
        }
    }
}