pipeline {
    agent any

    stages {
        stage('Clone Repositories') {
            steps {
                git branch: 'master', url: 'https://github.com/EllouzeEmna/DevopsProjet.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker-compose -f docker-compose.yml build'
            }
        }

        stage('Push to CI') {
            steps {
                sh 'scp -r . recette@192.168.43.207:/home/user/builds'
            }
        }

        stage('Deploy on CI') {
            steps {
                sshagent(['ssh-credentials-ci']) {
                    sh 'ssh recette@192.168.43.207 "cd /home/user/builds && docker-compose up -d"'
                }
            }
        }
    }
}
