pipeline {
    agent { label 'OPENMRS' }
    triggers { pollSCM('* * * * *') }
    stages {
        stage('clone_the_code') {
            steps {
                mail subject: 'Build Started',
                  body: 'Build started',
                  to: 'maheshmech9999@gmail.com'
                git branch: 'master',
                       url: 'https://github.com/maheshryali123/openmrs-core.git'
            }
        }
        //stage('Build_the_package') {
            //steps {
                //sh 'mvn clean package'
            //}
        //}
        //stage('sonar_scan') {
            //steps{
            //withSonarQubeEnv('sonar_scanner') {
                //sh 'mvn clean package sonar:sonar'
            //}
           //}
        //}
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
        stage('Exec Maven') {
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
       stage('Publish build info') {
           steps {
            rtPublishBuildInfo (
                serverId: "jfrogserver"
            )
           }
       }
    }
    post {
        always {
            echo 'Job completed'
            mail subject: 'build completed',
                  body: 'Build completed',
                    to: 'maheshmech9999@gmail.com'
        }
        failure {
            mail subject: 'Build failed',
                body: 'Build failed',
                  to: 'maheshmech9999@gmail.com'
        }
        success {
            junit '**/surefire-reports/*.xml'
        }
    }
}