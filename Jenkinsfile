import groovy.json.JsonOutput

pipeline {
    agent any
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
        stage('Create GitHub commit status') {
            steps {
                script {
                    // Define the status
                    def status = 'success ' // or 'failure', 'error', 'pending'
                    def description = 'The build completed successfully.' // or any other message
                    def context = 'continuous-integration/jenkins' // or any other context

                    // Define the GitHub API URL for the commit status
                    def apiUrl = "https://api.github.com/repos/Wousta/PROF-2023-Ejercicio4/statuses/${env.GIT_COMMIT}"

                    // Define the JSON payload
                    def payload = JsonOutput.toJson([
                        state: status,
                        description: description,
                        context: context
                    ])

                    // Send the request to the GitHub API
                    withCredentials([string(credentialsId: 'luisbToken', variable: 'GITHUB_TOKEN')]) {
                        sh """
                            curl -X POST -H "Authorization: token ${GITHUB_TOKEN}" \
                            -H "Content-Type: application/json" \
                            -d '${payload}' \
                            '${apiUrl}'
                        """
                    }
                }
            }
        }
    }
}