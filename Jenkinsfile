@Library('belajar-jenkins-shared-library@main') _
import cilestri.jenkins.Output

pipeline {
    agent any

    stages {
        stage('Hello Groovy') {
            steps {
                Output.hello('Groovy')
            }
        }

        stage('Global Variable') {
            steps {
                echo(author.name())
                echo(author.email())
            }
        }
    }
}
