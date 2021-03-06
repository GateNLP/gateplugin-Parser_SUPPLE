pipeline {
    agent any
    triggers {
        // rebuild this plugin if gate-top gets rebuilt so we can check we haven't introduced a breaking change
        upstream(upstreamProjects: "gate-top", threshold: hudson.model.Result.SUCCESS)
    }
    options {
        disableConcurrentBuilds()
    }
    stages {
       stage('Build') {
            steps {
                withAnt(installation: 'ANT-1.9.7', jdk: 'JDK1.8') {
                    sh 'ant clean test.plcafe'
                }
            }
            post {
                always {
                    warnings canRunOnFailed: true, consoleParsers: [[parserName: 'Java Compiler (javac)']], defaultEncoding: 'UTF-8', excludePattern: "**/test/**", failedNewAll: '0', unstableNewAll: '0', useStableBuildAsReference: true
                   //junit 'TEST-*.xml'
                }
            }
       }
       stage('Document') {
            when{
                expression { currentBuild.currentResult != "FAILED" && currentBuild.changeSets != null && currentBuild.changeSets.size() > 0 }
            }
            steps {
                withAnt(installation: 'ANT-1.9.7', jdk: 'JDK1.8') {
                    sh 'ant javadoc'
                }
            }
            post {
                success {
                    step([$class: 'JavadocArchiver', javadocDir: 'doc/javadoc', keepAll: false])
                }
            }
       }
       stage('Distro') {
            when{
                expression { currentBuild.currentResult != "FAILED" && currentBuild.changeSets != null && currentBuild.changeSets.size() > 0 }
            }
            steps {
                withAnt(installation: 'ANT-1.9.7', jdk: 'JDK1.8') {
                    sh 'ant dist'
                }
            }
            post {
                success {
                    archiveArtifacts artifacts:'gateplugin-Parser_SUPPLE-*.zip'
                }
            }
       }
    }
}
