pipeline {
    agent any 

    environment {
        IMAGE_PREFIX = "mamadoubadev"  // Remplace par ton DockerHub username
        DOCKER_CREDENTIALS_ID = "docker-hub-credentials"  // ID des credentials Jenkins pour DockerHub
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/maammadoubah-10/projetDevops.git'
                sh 'git pull origin main' // S'assurer que le code est à jour
            }
        }

        stage('Build & Push Docker Images') {
            steps {
                script {
                    def services = ['ms-classes', 'ms-professeurs', 'ms-emplois', 'ms-cours', 'ms-etudiants']

                    withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS_ID, 
                                                      usernameVariable: 'DOCKER_USER', 
                                                      passwordVariable: 'DOCKER_PASS')]) {
                        for (service in services) {
                            dir("devops/${service}") {
                                sh """
                                    echo "📌 Construction de l'image Docker pour ${service}..."
                                    docker build -t ${env.IMAGE_PREFIX}/${service}:latest .
                                    echo "📌 Login à DockerHub..."
                                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                                    echo "📌 Push de l'image sur DockerHub..."
                                    docker push ${env.IMAGE_PREFIX}/${service}:latest
                                """
                            }
                        }
                    }
                }
            }
        }

        stage('Deploy Docker Containers') {
            steps {
                script {
                    def services = ['ms-classes', 'ms-professeurs', 'ms-emplois', 'ms-cours', 'ms-etudiants']

                    for (service in services) {
                        sh """
                            echo "📌 Démarrage du conteneur ${service}..."
                            docker run -d --name ${service} -p 8080:80 ${env.IMAGE_PREFIX}/${service}:latest
                        """
                    }
                }
            }
        }
    }
}
