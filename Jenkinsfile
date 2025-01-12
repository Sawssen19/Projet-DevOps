pipeline {
    agent any
    tools {
        nodejs 'nodejs' // Configure Node.js dans Jenkins
    }
    environment {
        NEXUS_URL = "http://192.168.1.20:8081" // URL de Nexus
        NEXUS_REPOSITORY = "docker-hosted"     // Nom du repository Docker dans Nexus
        NEXUS_CREDENTIAL_ID = "nexus-user-credentials" // ID des credentials Jenkins pour Nexus
    }
    stages {
        stage("Clean up") {
            steps {
                deleteDir() // Nettoyer le workspace
            }
        }

        stage("Clone repo") {
            steps {
                sh "git clone https://github.com/Sawssen19/Projet-DevOps.git"
            }
        }

        stage("Build Frontend Image") {
            steps {
                dir("Projet-DevOps/react-crud-web-api-master") { // Chemin ajusté
                    sh "npm install"
                    sh "npm run build"
                    sh "docker build -t react-frontend:latest ."
                    sh "docker tag react-frontend:latest ${NEXUS_URL}/repository/${NEXUS_REPOSITORY}/react-frontend:latest"
                }
            }
        }

        stage("Build Backend Image") {
            steps {
                dir("Projet-DevOps/nodejs-express-sequelize-mysql-master") { // Chemin ajusté
                    sh "npm install"
                    sh "docker build -t node-backend:latest ."
                    sh "docker tag node-backend:latest ${NEXUS_URL}/repository/${NEXUS_REPOSITORY}/node-backend:latest"
                }
            }
        }

        stage("Push Images to Nexus") {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: NEXUS_CREDENTIAL_ID, usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                        sh "echo $NEXUS_PASS | docker login ${NEXUS_URL} --username $NEXUS_USER --password-stdin"
                        sh "docker push ${NEXUS_URL}/repository/${NEXUS_REPOSITORY}/react-frontend:latest"
                        sh "docker push ${NEXUS_URL}/repository/${NEXUS_REPOSITORY}/node-backend:latest"
                    }
                }
            }
        }

        stage("Run Docker Compose") {
            steps {
                dir("Projet-DevOps") {
                    sh "docker-compose up -d"
                }
            }
        }
    }
}

