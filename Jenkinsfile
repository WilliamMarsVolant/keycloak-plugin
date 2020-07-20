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
        stage('Build') {
            steps {
                configFileProvider([configFile(fileId: 'd1319e82-e302-446b-8e66-118dd2ee223f', variable: 'MVN_SETTINGS_FILE')]) {
                    echo 'Building...'
                    echo 'Adding settings file'
                    echo 'Running maven build'
                    sh 'mvn clean'
                    sh 'mvn build'
                }
            }
        }
        stage('Test') {
            environment {
                sonarScanner = tool name: 'SonarQube Scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
            }
            steps {
                withSonarQubeEnv('Sonarqube Di2e Server') {
                    sh '${sonarScanner}/bin/sonar-scanner -X'
                }
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}