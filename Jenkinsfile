pipeline {
    agent any

    stages {

        stage('DB management') {
            steps {
                script {
                    sh 'sqlite3 Employees.db .dump > dump.sql'
                    sh 'rm Employees.db'
                    sh 'sqlite3 Employees.db < sqlite.sql'
                    sh 'cat dump.sql | awk \'/^INSERT.*;$/\' | sqlite3 Employees.db'
                }
            }

            post {
                success {
                    echo 'Success on script execution!'
                }
                failure {
                    echo 'Failed to execute the script!'
                    sh 'rm Employees.db'
                    sh 'sqlite3 Employees.db < dump.sql'
                }
                always {
                    sh 'rm dump.sql'
                }
            }
        }
    }
}