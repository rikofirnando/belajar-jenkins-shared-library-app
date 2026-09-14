pipeline {
    agent none

    environment {
        AUTHOR = "Riko Firnando 2"
        EMAIL = "riko.firnando@example.com"
        WEB = "https://www.example.com"
        PHONE = "+62 812-3456-7890"
        JAVA_HOME = '/usr/lib/jvm/java-11-openjdk-amd64'
    }

    triggers {
        // cron('* * * * *')
        // pollSCM('* * * * *')
        upstream(upstreamProjects: 'job1, job2', threshold: hudson.model.Result.SUCCESS)
    }

    parameters {
        string(name: 'NAME', defaultValue: 'Guest', description: 'What is your name?')
        text(name: 'DESCRIPTION', defaultValue: '', description: 'Tell me about yourself')
        booleanParam(name: 'DEPLOY', defaultValue: false, description: 'Do you need to deploy now?')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'qa / staging', 'prod'], description: 'Select the environment?')
        password(name: 'SECRET', defaultValue: '', description: 'Encrypt your key')
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '3'))
        disableConcurrentBuilds()
        timeout(time: 10, unit: 'MINUTES')
        timestamps()
    }

    stages {
        stage('Parameters') {
            agent {
                label 'jenkins-agent-01'
            }

            steps {
                echo "Hello, ${params.NAME}!"
                echo "Description: ${params.DESCRIPTION}"
                echo "Deploy: ${params.DEPLOY}"
                echo "Environment: ${params.ENVIRONMENT}"
                echo "Secret: ${params.SECRET}"
            }
        }

        stage('Check Java') {
            agent {
                label 'jenkins-agent-01'
            }

            environment {
                APP = credentials("riko_rahasia")
            }

            steps {
                script {
                    echo '========================================'
                    echo 'INFORMASI GLOBAL VARIABLE JENKINS'
                    echo '========================================'

                    echo("Author        : ${env.AUTHOR}")
                    echo("Email         : ${env.EMAIL}")
                    echo("Website       : ${env.WEB}")
                    echo("Phone         : ${env.PHONE}")
                    echo("App User   : ${APP_USR}")
                    echo("App Password : ${APP_PSW}")

                    sh('echo "App Password : ${APP_PSW}" > "rahasia.txt"')

                    echo "Start Job     : ${env.JOB_NAME}"
                    echo "Build Number  : ${env.BUILD_NUMBER}"
                    echo "Build ID      : ${env.BUILD_ID}"
                    echo "Build Tag     : ${env.BUILD_TAG}"

                    echo '----------------------------------------'

                    echo "Node Jenkins  : ${env.NODE_NAME}"
                    echo "Label Node    : ${env.NODE_LABELS}"
                    echo "Workspace     : ${pwd()}"

                    echo '----------------------------------------'

                    echo "Branch Name   : ${env.GIT_BRANCH ?: env.BRANCH_NAME ?: 'Tidak tersedia'}"
                    echo "Git Commit    : ${env.GIT_COMMIT ?: 'Tidak tersedia'}"

                    echo '----------------------------------------'

                    echo "Jenkins URL   : ${env.JENKINS_URL ?: 'Tidak tersedia'}"
                    echo "Job URL       : ${env.JOB_URL ?: 'Tidak tersedia'}"
                    echo "Build URL     : ${env.BUILD_URL ?: 'Tidak tersedia'}"

                    echo '----------------------------------------'

                    echo "JAVA_HOME     : ${env.JAVA_HOME}"
                    echo "mvnw tersedia : ${fileExists('mvnw')}"
                    echo "pom.xml ada   : ${fileExists('pom.xml')}"

                    echo '========================================'
                }

                sh '''
                    echo "Checking Java and Maven..."

                    hostname
                    whoami
                    pwd

                    echo "JAVA_HOME=$JAVA_HOME"
                    echo "PATH=$PATH"

                    chmod +x mvnw

                    "$JAVA_HOME/bin/java" -version
                    ./mvnw -version
                '''
            }
        }

        stage('Clean') {
            agent {
                label 'jenkins-agent-01'
            }

            steps {
                script {
                    for (int i = 0; i < 5; i++) {
                        echo "Cleaning up... ${i + 1}"
                        sleep 1
                    }
                }

                sh '''
                    chmod +x mvnw
                    ./mvnw clean
                '''
            }
        }

        stage('Test') {
            agent {
                label 'jenkins-agent-01'
            }

            steps {
                script {
                    def data = [
                        firstName: 'John',
                        lastName : 'Doe',
                        age      : 30
                    ]

                    writeFile(
                        file: 'data.json',
                        text: groovy.json.JsonOutput.prettyPrint(
                            groovy.json.JsonOutput.toJson(data)
                        )
                    )

                    echo 'File data.json berhasil dibuat'
                }

                sh '''
                    chmod +x mvnw
                    ./mvnw test
                '''
            }
        }

        stage('Deploy') {
            input {
                message "Are you sure to proceed to the deploy stage?"
                ok "Yes, continue"
            }

            parameters {
                string(name: 'TARGET_ENV', defaultValue: 'dev', choices: ['dev', 'staging', 'prod'], description: 'Environment untuk deploy')
            }

            agent {
                label 'jenkins-agent-01'
            }

            steps {
                echo "Target environment for deploy: ${params.TARGET_ENV}"
                echo "Deploy dijalankan pada node: ${env.NODE_NAME}"
                echo 'Start deploying...'
                sleep 2
                echo 'Deploy completed...'
            }
        }

        stage('Release') {
            agent {
                label 'jenkins-agent-01'
            }

            steps {
                echo "Release dijalankan pada node: ${env.NODE_NAME}"
                echo "Target environment for release: ${params.TARGET_ENV}"
                echo 'Start releasing...'
                sleep 2
                echo 'Release completed...'
            }
        }

        stage('Cleanup') {
            agent {
                label 'jenkins-agent-01'
            }

            steps {
                echo "Cleanup dijalankan pada node: ${env.NODE_NAME}"
                echo "Target environment for cleanup: ${params.TARGET_ENV}"
                echo 'Cleaning up 1...'
                echo 'Cleaning up 2...'
            }
        }
    }

    post {
        always {
            echo 'This will always run'
            echo "Status akhir: ${currentBuild.currentResult}"
        }

        success {
            echo 'Pipeline berhasil'
        }

        failure {
            echo 'Pipeline gagal'
        }

        aborted {
            echo 'Pipeline dihentikan'
        }

        unstable {
            echo 'Pipeline selesai tetapi statusnya unstable'
        }

        changed {
            echo 'Status pipeline berubah dari build sebelumnya'
        }

        cleanup {
            echo 'Post cleanup selesai'
        }
    }
}