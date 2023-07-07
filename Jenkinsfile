pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Manual Approval') {
            steps {
                input(message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed', submitterParameter: 'APPROVER')
            }
        }
        stage('Deploy') {
            agent {
                docker {
                    image 'cdrx/pyinstaller-linux:python2'
                }
            }
            when {
                expression {
                    return env.APPROVER == 'Proceed'
                }
            }
            steps {
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
        stage('Delay') {
            steps {
                script {
                    sleep(time: 60, unit: 'SECONDS')
                }
            }
        }
        stage('Finish') {
            steps {
                echo 'Proses selesai.'
            }
        }
    }
}
