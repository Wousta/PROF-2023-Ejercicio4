pipeline {
    agent any
    environment {
        REPO_NAME = 'Wousta/PROF-2023-Ejercicio4'
    }
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
                    githubNotify(
                        def gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                        withCredentials([string(credentialsId: 'luisbToken', variable: 'GITHUB_ACCESS_TOKEN')]) {
                        sh '''
                            curl -X POST -H "Authorization: token $GITHUB_ACCESS_TOKEN" \
                            -H "Content-Type: application/json" \
                            -d '{"state":"success", "context":"Jenkins CI", "description":"Build Status", "target_url":"$BUILD_URL"}' \
                            "https://api.github.com/repos/${REPO_NAME}/statuses/${gitCommit}"
                        '''
                        }
                    )
                }
            }
        }
    }
}