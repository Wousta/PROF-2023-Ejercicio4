pipeline {
    // Define stages and steps here
    agent any
    stages {
        stage('Backup') {
            steps {
                echo 'Building..'
            }
        }
        stage('DropSchema') {
            steps {
                echo 'Testing..'
            }
        }
        stage('newSchema') {
            steps {
                echo 'Deploying....'
            }
        }
        stage('Restore') {
            steps {
                echo 'Deploying....'
            }
        }   
    }
}
