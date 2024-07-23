pipeline {

    agent any

    parameters {
        choice choices: ['chrome', 'firefox'], description: 'Select the browser name', name: 'BROWSER'
    }

    stages {

        stage('Start Grid') {
            steps {
                echo "Starting Grid with ${params.BROWSER}"
                bat "docker-compose -f grid.yaml up --scale ${params.BROWSER}=2 -d --verbose"
            }
        }

        stage('Run Test') {
            steps {
                echo "Running Tests"
                bat "docker-compose -f test-suites.yaml up --pull=always --verbose"
                script {
                    echo "Checking for failed tests"
                    if (fileExists('output/test-output/testng-failed.xml')) {
                        error('Failed test found')
                    }
                }
            }
        }

    }

    post {
        always {
            echo "Cleaning up Docker containers"
            bat "docker-compose -f grid.yaml down --verbose"
            bat "docker-compose -f test-suites.yaml down --verbose"
            echo "Archiving test reports"
            archiveArtifacts artifacts: 'output/test-output/emailable-report.html', followSymlinks: false
        }
    }

}
