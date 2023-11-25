pipeline {
    // agent any
    // tools {
    //     maven 'maven3.6.1'
    // }
    agent {
        docker {
            image 'maven:3.6.3-jdk-11'
        }
    }
    stages {
        // stage('Checkout') {
        //     steps {
        //         git branch: 'master',
        //         credentialsId: 'bitbucket-credentials',
        //         url: 'https://danycenas@bitbucket.org/danycenas/spring-petclinic-rest.git'
        //     }
        // }
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests -B -ntp'
            }
            post {
                success {
                    jacoco()
                }
            }
        }
        stage('Sonarqube') {
            steps {
                withCredentials([file(credentialsId: 'sonarqube-settings', variable: 'M2_SETTINGS')]) {
                    sh "mvn sonar:sonar -B -ntp -s ${M2_SETTINGS}"
                }
            }
        }
        stage('Artifactory') {
            steps {

                script {
                    // Forma 1 - Artifactory

                    sh 'printenv'

                    // env.MAVEN_HOME = '/usr/share/maven'

                    // def releases = 'spring-petclinic-rest-release'
                    // def snapshots = 'spring-petclinic-rest-snapshot'

                    // def server = Artifactory.server 'artifactory'

                    // def rtMaven = Artifactory.newMavenBuild()

                    // rtMaven.deployer server: server, releaseRepo: releases, snapshotRepo: snapshots

                    // def buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean package -B -ntp -DskipTests'

                    // server.publishBuildInfo buildInfo

                    // Forma 2 - File Spec

                    // def server = Artifactory.server 'artifactory'
                    // def repository = 'spring-petclinic-rest'

                    // if ("${GIT_BRANCH}" == 'origin/master') {
                    //     repository = repository + '-release'
                    // } else {
                    //     repository = repository + '-snapshot'
                    // }

                    // def uploadSpec = """
                    //     {
                    //         "files": [
                    //             {
                    //                 "pattern": "target/.*.jar",
                    //                 "target": "${repository}",
                    //                 "regexp": "true"
                    //             }
                    //         ]
                    //     }
                    // """
                    // server.upload spec: uploadSpec
                }
            }
        }
    }
    post {
        success {
            archiveArtifacts artifacts: 'target/*.jar, target/*.original', fingerprint: true
            deleteDir()
        }
    }
}
