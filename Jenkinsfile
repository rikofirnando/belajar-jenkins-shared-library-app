@Library('belajar-jenkins-shared-library@main') _

import cilestri.jenkins.Output

pipeline {
    agent any
    stages {
        stage('Hello Groovy') {
            steps {
                script {
                    Output.hello('Groovy')
                }
            }
        }
    }
}
