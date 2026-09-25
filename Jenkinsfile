@Library('belajar-jenkins-shared-library@main') _
import cilestri.jenkins.Output

pipeline {
    agent any

    stages {
        stage('Library Resource') {
            steps {
                script {
                    def config = libraryResource('resources/config/build.json')
                    echo(config)
                }
            }
        }

        stage('Hello Groovy') {
            steps {
                script {
                    Output.hello(this, 'Groovy')
                }
            }
        }

        stage('Hello Persons') {
            steps {
                script {
                    hello.person([
                        firstName: 'Riko',
                        lastName: 'Firnando'
                    ])
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
