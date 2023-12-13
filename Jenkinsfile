pipeline {
    agent any
    /*
    Make a backup of the current data in the database.
    Delete the current schema. Perhaps a DROP DATABASE could be performed.
    Load the new schema.
    Restore the previously backed up data.
    */
    stages {
        stage('Backup') {
            steps {
                echo 'Backup'
                sh "sqlite3 Employees.db '.backup Backup.db'"
                sh "cat Backup.db"
            }
        }
        stage('Delete old and create new') {
            steps {
                // Remove old database file
                sh "rm Employees.db"

                // Create new database using the SQL script
                sh "sqlite3 Employees.db < sqlite.sql"
            }
        }
        stage('Restore') {
            steps {
                echo 'Restore'
                sh "sqlite3 Employees.db '.restore Backup.db'"
                sh "cat Employees.db"
            }
        }
        stage('Update GitHub Status') {
            steps {
                script {
                    // Update GitHub status as per build result
                    def gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                    githubNotify(
                        repo: 'Wousta/PROF-2023-Ejercicio4',
                        commitSha: gitCommit,
                        credentialsId: env.luisbToken,
                        errorHandlers: [[$class: 'ChangingBuildStatusErrorHandler']],
                        statusResult: currentBuild.currentResult,
                        statusContext: 'Jenkins CI',
                        statusDescription: 'Build Status',
                        targetUrl: env.BUILD_URL
                    )
                }
            }
        }
    }
}