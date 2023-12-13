node {
    def REPO_NAME = 'Wousta/PROF-2023-Ejercicio4'
    /*
    Make a backup of the current data in the database.
    Delete the current schema. Perhaps a DROP DATABASE could be performed.
    Load the new schema.
    Restore the previously backed up data.
    */
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
            def gitCommit = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
        
            // Retrieve GitHub token from Jenkins credentials
            def token = credentials('luisbToken')

            def curlCmd = "curl -X POST -H 'Authorization: token ${token}' \
                            -H 'Content-Type: application/json' \
                            -d '{\"state\":\"success\", \"context\":\"Jenkins CI\", \"description\":\"Build Status\", \"target_url\":\"${env.BUILD_URL}\"}' \
                            https://api.github.com/repos/${REPO_NAME}/statuses/${gitCommit}"

            sh(curlCmd)
        }
    
}