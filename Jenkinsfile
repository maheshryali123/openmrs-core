pipeline {
    agent { label 'OPENMRS' }
    triggers { POLLISCM('* * * * *') }
    stages {
        stage('clone_the_code') {
            steps {
                git branch: 'master',
                       url: 'https://github.com/maheshryali123/openmrs-core.git'
            }
        }
        stage('Build_the_package') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('sonar_scan') {
            steps{
            withSonarQubeEnv('sonar_scanner') {
                sh 'mvn clean package sonar:sonar'
            }
            }
        }
    }
}