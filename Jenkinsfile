pipeline {
    agent any
    tools { 
        maven 'Maven 3.3.9' 
        jdk 'JDK 8u5' 
    }
    stages {
        stage('Build') {
            steps {
                configFileProvider([configFile(fileId: 'd1319e82-e302-446b-8e66-118dd2ee223f', variable: 'MVN_SETTINGS_FILE')]) {
                    echo 'Building...'
                    echo 'Adding settings file'
                    echo 'Running maven build'
                    sh 'mvn verify'
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}