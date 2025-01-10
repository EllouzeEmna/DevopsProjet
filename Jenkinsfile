pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = 'emnaellouze123487'  // Votre nom d'utilisateur Docker Hub
        DOCKERHUB_PASSWORD = credentials('ssh-credentials-ci')  // ID de vos credentials Docker Hub
        VM2_USER = 'recette'           // Utilisateur SSH pour la VM2
        VM2_IP = '192.168.43.207'      // Adresse IP de la VM2
        VM2_APP_PATH = '/home/recette/app'  // Répertoire de déploiement sur la VM2
        DOCKER_CLI_EXPERIMENTAL = "enabled"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/EllouzeEmna/DevopsProjet.git', branch: 'master'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker-compose build'
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                docker-compose up -d
                docker-compose run --rm backend mvn test
                docker-compose down
                '''
            }
        }

        stage('Push Docker Images to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        sh 'docker-compose push'
                    }
                }
            }
        }

          stage('Deploy on VM2') {
            steps {
                sshagent(['ssh-credentials-ci']) {
                    sh '''
                    ssh $VM2_USER@$VM2_IP "mkdir -p $VM2_APP_PATH"
                    scp docker-compose.yml $VM2_USER@$VM2_IP:$VM2_APP_PATH/
                    ssh $VM2_USER@$VM2_IP "
                        cd $VM2_APP_PATH &&
                        docker-compose pull &&
                        docker-compose up -d
                    "
                    '''
                }
            }
        }
    }

    post {
        always {
            cleanWs()  // Nettoyer le workspace après l'exécution
        }
    }
}
