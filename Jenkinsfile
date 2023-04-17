pipeline {
    agent { label 'OPENMRS' }
    triggers { pollSCM('* * * * *') }
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
        stage('push to artifactory') {
            steps {
                rtServer (
                    id: "jfrogserver",
                    url: "https://projectsunique.jfrog.io/",
                    crendentialsId: CREDENTIALS
                )
                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverid: "jfrogserver",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )
            }
        }
        stage ('Exec Maven') {
            steps {
                rtMavenRun (
                    tool: MAVEN_TOOL,
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }     
    }
}
}