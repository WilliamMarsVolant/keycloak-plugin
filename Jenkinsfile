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
    stages {
        stage('MavenBuild') {
            steps {
                configFileProvider([configFile(fileId: 'd1319e82-e302-446b-8e66-118dd2ee223f', variable: 'MVN_SETTINGS_FILE')]) {
                    echo 'Building...'
                    echo 'Adding settings file'
                    echo 'Running maven build'
                    sh 'mvn package'
                }
            }
        }
        stage('SonarQubeAnalysis') {
            environment {
                sonarScanner = tool name: 'SonarQube Scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
            }
            steps {
                withSonarQubeEnv('Sonarqube Di2e Server') {
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
        stage('Deploy') {
            steps {
                sh 'mvn deploy:deploy-file -DgneratePom=false -DrepoisotryId=nexus -Durl=https://nexus.di2e.net/nexus3/repository/Public_DI2E_Maven/ -DpomFile=pom.xml -Dfile=target/keycloak.jar'
            }
            post {
                failure {
                    subject: "Deployment FAILED Build: ${env.BUILD_ID}",
                        body: "Job Name: ${env.JOB_NAME} \nBuild Number: ${env.BUILD_NUMBER}",
                        to:"william.mars@di2e.net"
                }
            }
        }
    }
}