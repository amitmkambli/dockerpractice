pipeline{

    agent any

    parameters {
        choice choices: ['chrome', 'firefox'], description: 'Select the browser name', name: 'BROWSER'
    }


    stages{

        stage('Start Grid'){
            steps{
                bat "docker-compose -f grid.yaml up --scale ${params.BROWSER}=2 -d"
            }
        }

        stage('Run Test'){
            steps{
                bat "docker-compose -f test-suites.yaml up --pull=always"
                script{
                    if(fileExists('output/test-output/testng-failed.xml')){
                        error('failed test found')
                    }
                }
            }
        }

    }

    post {
        always {
            bat "docker-compose -f grid.yaml down"
            bat "docker-compose -f test-suites.yaml down"
            archiveArtifacts artifacts: 'output/test-output/emailable-report.html', followSymlinks: false
            archiveArtifacts artifacts: 'output/reports/Extent_Report.html', followSymlinks: false
        }
    }

}