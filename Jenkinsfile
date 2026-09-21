pipeline {
    agent none

    environment {
        AUTHOR = 'Riko Firnando 2'
        EMAIL = 'riko.firnando@example.com'
        WEB = 'https://www.example.com'
        PHONE = '+62 812-3456-7890'
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
                APP = credentials('riko_rahasia')
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

                    sh '''
                        echo "App Password : $APP_PSW" > "rahasia.txt"
                    '''

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
            agent {
                label 'jenkins-agent-01'
            }

            input {
                message 'Are you sure to proceed to the deploy stage?'
                ok 'Yes, continue'

                parameters {
                    choice(
                        name: 'TARGET_ENV',
                        choices: ['dev', 'staging', 'prod'],
                        description: 'Environment untuk deploy'
                    )
                }
            }

            steps {
                echo "Target environment for deploy: ${env.TARGET_ENV}"
                echo "Deploy dijalankan pada node: ${env.NODE_NAME}"
                echo 'Start deploying...'

                sleep 2

                echo 'Deploy completed...'
            }
        }

        stage('Release') {
            when {
                beforeAgent true
                expression { return params.DEPLOY == true }
            }

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

        stage('Release v2') {
            when {
                beforeAgent true
                expression { return params.DEPLOY == true }
            }

            agent {
                label 'jenkins-agent-01'
            }

            steps {
                withCredentials([usernamePassword(
            credentialsId: 'eko_rahasia',
            usernameVariable: 'RELEASE_USER',
            passwordVariable: 'RELEASE_PASSWORD'
        )]) {
                    sh '''
                set +x

                echo "Credentials berhasil dimuat"
                echo "Username dan password siap digunakan"
                echo "Simulasi release selesai"
            '''
        }
            }
        }

        // Materi baru: beberapa stage anak berjalan berurutan.
        stage('Sequential Stages') {
            agent {
                label 'jenkins-agent-01'
            }

            stages {
                stage('Sequential 1 - Persiapan') {
                    steps {
                        echo "Langkah 1: Persiapan pada node ${env.NODE_NAME}"
                    }
                }

                stage('Sequential 2 - Verifikasi') {
                    steps {
                        echo "Langkah 2: Verifikasi pada node ${env.NODE_NAME}"
                    }
                }

                stage('Sequential 3 - Selesai') {
                    steps {
                        echo "Langkah 3: Selesai pada node ${env.NODE_NAME}"
                    }
                }
            }
        }

        // Materi baru: beberapa stage dijalankan secara bersamaan.
        stage('Parallel Stages') {
            failFast true

            parallel {
                stage('Parallel 1 - Prepare Java') {
                    agent {
                        label 'jenkins-agent-01'
                    }

                    steps {
                        echo "Prepare Java dijalankan pada node: ${env.NODE_NAME}"
                        echo 'Memulai pengecekan Java...'
                        sh '"$JAVA_HOME/bin/java" -version'
                        sleep 5
                        echo 'Prepare Java selesai'
                    }
                }

                stage('Parallel 2 - Prepare Maven') {
                    agent {
                        label 'jenkins-agent-01'
                    }

                    steps {
                        echo "Prepare Maven dijalankan pada node: ${env.NODE_NAME}"
                        echo 'Memulai pengecekan Maven Wrapper...'
                        sh '''
                            chmod +x mvnw
                            ./mvnw -version
                        '''
                        sleep 5
                        echo 'Prepare Maven selesai'
                    }
                }

                stage('Parallel 3 - Check Project') {
                    agent {
                        label 'jenkins-agent-01'
                    }

                    steps {
                        echo "Check Project dijalankan pada node: ${env.NODE_NAME}"

                        script {
                            echo "pom.xml tersedia   : ${fileExists('pom.xml')}"
                            echo "mvnw tersedia      : ${fileExists('mvnw')}"
                            echo "data.json tersedia : ${fileExists('data.json')}"
                        }

                        sleep 5
                        echo 'Check Project selesai'
                    }
                }
            }
        }

        stage('Matrix Testing') {
            matrix {
                axes {
                    axis {
                        name 'MATRIX_ENV'
                        values 'dev', 'staging'
                    }

                    axis {
                        name 'TEST_TYPE'
                        values 'unit', 'api'
                    }
                }

                agent {
                    label 'jenkins-agent-01'
                }

                stages {
                    stage('Preparation') {
                        steps {
                            echo 'Melakukan persiapan'
                        }
                    }

                    stage('Execution') {
                        steps {
                            echo 'Menjalankan pengujian'
                        }
                    }
                }
            }
        }

        stage('Simple Matrix Test') {
            matrix {
                axes {
                    axis {
                        name 'TEST_TYPE'
                        values 'unit', 'integration', 'api'
                    }
                }

                agent {
                    label 'jenkins-agent-01'
                }

                stages {
                    stage('Show Matrix Cell') {
                        steps {
                            echo '========================================'
                            echo "Jenis test : ${TEST_TYPE}"
                            echo "Node       : ${env.NODE_NAME}"
                            echo "Workspace  : ${env.WORKSPACE}"
                            echo '========================================'
                        }
                    }

                    stage('Run Test') {
                        steps {
                            echo "Menjalankan ${TEST_TYPE} test..."
                            sleep 2
                        }
                    }
                }
            }
        }

        stage('Matrix Java Version Test') {
            matrix {
                axes {
                    axis {
                        name 'TEST_TYPE'
                        values 'unit', 'api'
                    }

                    axis {
                        name 'JAVA_VERSION'
                        values '11', '17'
                    }
                }

                agent {
                    label 'jenkins-agent-01'
                }

                stages {
                    stage('Show Configuration') {
                        steps {
                            echo '========================================'
                            echo "Test Type    : ${TEST_TYPE}"
                            echo "Java Version : ${JAVA_VERSION}"
                            echo "Node         : ${env.NODE_NAME}"
                            echo '========================================'
                        }
                    }

                    stage('Execute Test') {
                        steps {
                            echo "Menjalankan ${TEST_TYPE} menggunakan Java ${JAVA_VERSION}"
                        }
                    }
                }
            }
        }

        stage('Matrix Build') {
            matrix {
                axes {
                    axis {
                        name 'TEST_TYPE'
                        values 'unit', 'api'
                    }
                }

                agent {
                    label 'jenkins-agent-01'
                }

                stages {
                    stage('Preparation') {
                        steps {
                            echo "Persiapan ${TEST_TYPE}"
                        }
                    }

                    stage('Testing') {
                        steps {
                            echo "Menjalankan ${TEST_TYPE}"
                        }
                    }

                    stage('Report') {
                        steps {
                            echo "Membuat laporan ${TEST_TYPE}"
                        }
                    }
                }
            }
        }

        stage('Matrix Environment Test') {
            matrix {
                axes {
                    axis {
                        name 'TEST_TYPE'
                        values 'unit', 'integration', 'api'
                    }

                    axis {
                        name 'TARGET_ENV'
                        values 'dev', 'staging'
                    }
                }

                excludes {
                    exclude {
                        axis {
                            name 'TEST_TYPE'
                            values 'integration'
                        }

                        axis {
                            name 'TARGET_ENV'
                            values 'dev'
                        }
                    }
                }

                agent {
                    label 'jenkins-agent-01'
                }

                stages {
                    stage('Show Matrix Cell') {
                        steps {
                            echo '========================================'
                            echo "Jenis Test  : ${TEST_TYPE}"
                            echo "Environment : ${TARGET_ENV}"
                            echo "Node        : ${env.NODE_NAME}"
                            echo '========================================'
                        }
                    }

                    stage('Run Test') {
                        steps {
                            echo "Menjalankan ${TEST_TYPE} test pada ${TARGET_ENV}"
                            sleep 2
                        }
                    }
                }
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
