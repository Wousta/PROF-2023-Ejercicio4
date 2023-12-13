def postGithubStatus(String status, String description) {
    // Define the context
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
        sh '''
            curl -X POST -H "Authorization: token $GITHUB_TOKEN" \
            -H "Content-Type: application/json" \
            -d ''' + "'${payload}'" + ''' \
            ''' + "'${apiUrl}'"
    }
}

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
    }
    post {
        success {
            postGithubStatus('success', 'All stages completed successfully.')
        }
        failure {
            postGithubStatus('failure', 'One or more stages failed.')
        }
        unstable {
            postGithubStatus('error', 'One or more stages completed with errors.')
        }
    }
}