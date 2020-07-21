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
        stage('Deploy') {
            steps {
                    echo 'Deploying to Nexus'
                    // sh 'mvn deploy:deploy-file -DgneratePom=false -DrepositoryId=mavenPublic -Durl=https://nexus.di2e.net/nexus3/repository/Public_DI2E_Maven/ -DpomFile=pom.xml -Dfile=target/keycloak.jar'
                    sh '''
                        mvn deploy:deploy-file -DartifactId=keycloakPluginWilliam \
                        -Dpackageing=.jar \
                        -Dfile=target/keycloak.jar \
                        -DrepositoryId=mavenPublic \
                        -Durl=https://nexus.di2e.net/nexus3/repository/Public_DI2E_Maven/
                        '''
            }
        }
    }
}