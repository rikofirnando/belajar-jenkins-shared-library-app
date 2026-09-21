// Jenkinsfile Declarative Pipeline
// Tujuan: contoh pipeline pembelajaran yang rapi, aman, dan berurutan.

pipeline {
    // Tidak mengunci satu agent untuk seluruh pipeline.
    // Setiap stage menentukan agent-nya sendiri agar mudah dipelajari.
    agent none

    // Variabel global yang dapat digunakan oleh seluruh stage.
    environment {
        AUTHOR    = 'Riko Firnando'
        EMAIL     = 'riko.firnando@example.com'
        WEB       = 'https://www.example.com'
        PHONE     = '+62 812-3456-7890'
        JAVA_HOME = '/usr/lib/jvm/java-11-openjdk-amd64'

        // Menambahkan Java ke PATH tanpa menghapus PATH bawaan agent.
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
    }

    // Pemicu otomatis pipeline.
    triggers {
        // Aktifkan salah satu jika dibutuhkan:
        // cron('H * * * *')
        // pollSCM('H/5 * * * *')

        // Pipeline berjalan setelah job1 atau job2 sukses.
        upstream(
            upstreamProjects: 'job1, job2',
            threshold: hudson.model.Result.SUCCESS
        )
    }

    // Form parameter yang muncul saat memilih "Build with Parameters".
    parameters {
        string(
            name: 'NAME',
            defaultValue: 'Guest',
            description: 'Nama pengguna'
        )
        text(
            name: 'DESCRIPTION',
            defaultValue: '',
            description: 'Deskripsi singkat'
        )
        booleanParam(
            name: 'DEPLOY',
            defaultValue: false,
            description: 'Jalankan proses deploy dan release?'
        )
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'staging', 'prod'],
            description: 'Target environment'
        )
    }

    // Pengaturan umum pipeline.
    options {
        // Hanya menyimpan tiga build terakhir.
        buildDiscarder(logRotator(numToKeepStr: '3'))

        // Mencegah dua build job ini berjalan bersamaan.
        disableConcurrentBuilds()

        // Menghentikan pipeline jika melebihi 10 menit.
        timeout(time: 10, unit: 'MINUTES')

        // Menambahkan waktu pada setiap baris log.
        timestamps()
    }

    stages {
        // 1. Menampilkan parameter yang dipilih pengguna.
        stage('1 - Show Parameters') {
            agent { label 'jenkins-agent-01' }

            steps {
                echo "Hello, ${params.NAME}!"
                echo "Description : ${params.DESCRIPTION}"
                echo "Deploy      : ${params.DEPLOY}"
                echo "Environment : ${params.ENVIRONMENT}"
            }
        }

        // 2. Memeriksa informasi Jenkins, Java, dan Maven Wrapper.
        stage('2 - Check Environment') {
            agent { label 'jenkins-agent-01' }

            steps {
                script {
                    echo '========================================'
                    echo 'INFORMASI PIPELINE JENKINS'
                    echo '========================================'
                    echo "Author       : ${env.AUTHOR}"
                    echo "Email        : ${env.EMAIL}"
                    echo "Website      : ${env.WEB}"
                    echo "Phone        : ${env.PHONE}"
                    echo "Job          : ${env.JOB_NAME}"
                    echo "Build Number : ${env.BUILD_NUMBER}"
                    echo "Node         : ${env.NODE_NAME}"
                    echo "Node Labels  : ${env.NODE_LABELS}"
                    echo "Workspace    : ${pwd()}"
                    echo "Branch       : ${env.GIT_BRANCH ?: env.BRANCH_NAME ?: 'Tidak tersedia'}"
                    echo "Git Commit   : ${env.GIT_COMMIT ?: 'Tidak tersedia'}"
                    echo "JAVA_HOME    : ${env.JAVA_HOME}"
                    echo "mvnw ada     : ${fileExists('mvnw')}"
                    echo "pom.xml ada  : ${fileExists('pom.xml')}"
                    echo '========================================'
                }

                // Menjalankan pemeriksaan langsung pada shell agent.
                sh '''
                    set -eu

                    echo "Checking agent, Java, and Maven..."
                    hostname
                    whoami
                    pwd

                    test -f pom.xml
                    test -f mvnw
                    chmod +x mvnw

                    java -version
                    ./mvnw -version
                '''
            }
        }

        // 3. Contoh penggunaan credential bertipe Username with password.
        stage('3 - Check Credentials') {
            agent { label 'jenkins-agent-01' }

            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'riko_rahasia',
                        usernameVariable: 'APP_USER',
                        passwordVariable: 'APP_PASSWORD'
                    )
                ]) {
                    // Jangan echo atau simpan password ke file/workspace.
                    sh '''
                        set +x
                        test -n "$APP_USER"
                        test -n "$APP_PASSWORD"
                        echo "Credential berhasil dimuat dengan aman"
                    '''
                }
            }
        }

        // 4. Membersihkan hasil build sebelumnya.
        stage('4 - Clean') {
            agent { label 'jenkins-agent-01' }

            steps {
                echo 'Membersihkan hasil build sebelumnya...'
                sh '''
                    set -eu
                    chmod +x mvnw
                    ./mvnw clean
                '''
            }
        }

        // 5. Membuat data contoh dan menjalankan automated test Maven.
        stage('5 - Test') {
            agent { label 'jenkins-agent-01' }

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
                    set -eu
                    chmod +x mvnw
                    ./mvnw test
                '''
            }
        }

        // 6. Contoh nested stages yang berjalan berurutan.
        stage('6 - Sequential Stages') {
            agent { label 'jenkins-agent-01' }

            stages {
                stage('6.1 - Preparation') {
                    steps {
                        echo "Persiapan pada node ${env.NODE_NAME}"
                    }
                }

                stage('6.2 - Verification') {
                    steps {
                        echo "Verifikasi pada node ${env.NODE_NAME}"
                    }
                }

                stage('6.3 - Finish') {
                    steps {
                        echo 'Sequential stages selesai'
                    }
                }
            }
        }

        // 7. Contoh beberapa pekerjaan yang berjalan bersamaan.
        stage('7 - Parallel Checks') {
            failFast true // Hentikan cabang lain jika satu cabang gagal.

            parallel {
                stage('7.1 - Check Java') {
                    agent { label 'jenkins-agent-01' }

                    steps {
                        echo "Check Java pada node ${env.NODE_NAME}"
                        sh 'java -version'
                    }
                }

                stage('7.2 - Check Maven') {
                    agent { label 'jenkins-agent-01' }

                    steps {
                        echo "Check Maven pada node ${env.NODE_NAME}"
                        sh '''
                            set -eu
                            chmod +x mvnw
                            ./mvnw -version
                        '''
                    }
                }

                stage('7.3 - Check Project Files') {
                    agent { label 'jenkins-agent-01' }

                    steps {
                        script {
                            echo "pom.xml ada   : ${fileExists('pom.xml')}"
                            echo "mvnw ada      : ${fileExists('mvnw')}"
                            echo "data.json ada : ${fileExists('data.json')}"
                        }
                    }
                }
            }
        }

        // 8. Matrix membuat kombinasi TEST_TYPE x TARGET_ENV.
        // Total awal: 3 x 2 = 6 kombinasi, lalu 1 kombinasi dikecualikan.
        stage('8 - Matrix Testing') {
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

                // Integration test pada dev tidak dijalankan.
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

                agent { label 'jenkins-agent-01' }

                stages {
                    stage('8.1 - Show Matrix Cell') {
                        steps {
                            echo "Test        : ${TEST_TYPE}"
                            echo "Environment : ${TARGET_ENV}"
                            echo "Node        : ${env.NODE_NAME}"
                        }
                    }

                    stage('8.2 - Execute Matrix Test') {
                        steps {
                            // Masih berupa simulasi pembelajaran.
                            // Ganti echo dengan command test yang sebenarnya.
                            echo "Menjalankan ${TEST_TYPE} test pada ${TARGET_ENV}"
                        }
                    }

                    stage('8.3 - Create Report') {
                        steps {
                            echo "Membuat laporan ${TEST_TYPE} untuk ${TARGET_ENV}"
                        }
                    }
                }
            }
        }

        // 9. Deploy hanya muncul jika parameter DEPLOY dicentang.
        stage('9 - Deploy') {
            when {
                beforeAgent true
                expression { params.DEPLOY }
            }

            agent { label 'jenkins-agent-01' }

            // Meminta konfirmasi manual sebelum steps dijalankan.
            input {
                message "Deploy ke ${params.ENVIRONMENT}?"
                ok 'Ya, lanjutkan'
            }

            steps {
                echo "Deploy ke ${params.ENVIRONMENT} pada ${env.NODE_NAME}"
                echo 'Deploy selesai (simulasi)'
            }
        }

        // 10. Release dijalankan setelah Deploy dan memakai target yang sama.
        stage('10 - Release') {
            when {
                beforeAgent true
                expression { params.DEPLOY }
            }

            agent { label 'jenkins-agent-01' }

            steps {
                echo "Release ke ${params.ENVIRONMENT} pada ${env.NODE_NAME}"
                echo 'Release selesai (simulasi)'
            }
        }
    }

    // Aksi penutup berdasarkan hasil pipeline.
    post {
        always {
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
            echo 'Pipeline selesai dengan status unstable'
        }

        changed {
            echo 'Status pipeline berubah dari build sebelumnya'
        }

        cleanup {
            echo 'Post cleanup selesai'
        }
    }
}
