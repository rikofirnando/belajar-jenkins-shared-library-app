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
    }

    stages {
        stage('Global Variable') {
            steps {
                echo(author.name())
                echo(author.email())
            }
        }
    }
}
