pipeline {

    agent any

    parameters {
        choice choices: ['chrome', 'firefox'], description: 'Select the browser', name: 'BROWSER'
    }

    stages {
        stage('Start Grid') {
            steps {
                sh "docker-compose -f grid.yaml up --scale ${params.BROWSER}=2 -d"
            }
        }

        stage('Run Test') {
            steps {
                sh "docker-compose -f test-suites.yaml up --pull=always"
                script {
                    if(fileExists('output/regression-suite/testng-failed.xml')) {
                        error('failed test found')
                    }
                }
            }
        }
    }

    post {
        always {
            sh "docker-compose -f grid.yaml down"
            sh "docker-compose -f test-suites.yaml down"
            archiveArtifacts artifacts: 'output/regression-suite/emailable-report.html', followSymlinks: false
        }
    }
}