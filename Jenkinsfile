@Library('belajar-jenkins-shared-library@main') _
import cilestri.jenkins.Output

pipeline {
    agent any

    stages {
        stage('Hello Groovy') {
            steps {
                script {
                    Output.hello(this, 'Groovy')
                }
            }
        }

        stage('Global Variable') {
            steps {
                script {
                    echo author()
                    echo author.name()
                    echo author.email()
                }
            }
        }

        stage('Maven Compile') {
            steps {
                script {
                    maven(['clean', 'compile', 'test'])
                }
            }
        }
    }
}
