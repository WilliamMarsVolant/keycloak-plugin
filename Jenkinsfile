pipeline {
    agent {
        node {
            label 'docker'
        }
    }
    stages {
        stage('Build') {
            echo 'Building...'
            echo 'Adding settings file'
            configFileProvider([configFiles(fileID: 'd1319e82-e302-446b-8e66-118dd2ee223f', variable: 'MVN_SETTINGS_FILE')]) {
                echo 'Running maven build'
                sh 'mvn verify'
            }
        }
        stage('Test') {
            echo 'Testing...'
        }
        stage('Deploy') {
            echo 'Deploying...'
        }
    }
}