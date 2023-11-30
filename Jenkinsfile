pipeline {
    agent any

    stages {

        stage('DB management ') {
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
                    githubCommitStatus('SUCCESS', 'Jenkins build passed.')
                }
                failure {
                    githubCommitStatus('FAILURE', 'Jenkins build failed.')
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