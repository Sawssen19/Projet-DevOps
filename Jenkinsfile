pipeline {
    agent any
    tools {
        nodejs 'nodejs' // Configure Node.js dans Jenkins
       
    }

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "http://192.168.1.20:8083"  // URL de votre Nexus
        NEXUS_REPOSITORY = "docker-hosted"     // Le repository Docker dans Nexus
        NEXUS_CREDENTIAL_ID = "nexus-user-credentials" // L'ID des credentials Nexus
        FRONTEND_IMAGE = "react-frontend"
        BACKEND_IMAGE = "node-backend"
    }

    stages {
        stage("Clean up") {
            steps {
                deleteDir()
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
                    sh "docker build -t ${FRONTEND_IMAGE}:latest ."
                }
            }
        }

        stage("Build Backend Image") {
            steps {
                dir("Projet-DevOps/nodejs-express-sequelize-mysql-master") { // Chemin ajusté
                    sh "npm install"
                    sh "docker build -t ${BACKEND_IMAGE}:latest ."
                }
            }
        }

        stage("Push Frontend Image to Nexus") {
            steps {
                script {
                    // Connexion à Nexus Docker Registry et push de l'image frontend
                    withDockerRegistry([credentialsId: "${NEXUS_CREDENTIAL_ID}", url: "${NEXUS_URL}"]) {
                        sh "docker tag ${FRONTEND_IMAGE}:latest ${NEXUS_REPOSITORY}/${FRONTEND_IMAGE}:latest"
                        sh "docker push ${NEXUS_REPOSITORY}/${FRONTEND_IMAGE}:latest"
                    }
                }
            }
        }

        stage("Push Backend Image to Nexus") {
            steps {
                script {
                    // Connexion à Nexus Docker Registry et push de l'image backend
                    withDockerRegistry([credentialsId: "${NEXUS_CREDENTIAL_ID}", url: "${NEXUS_URL}"]) {
                        sh "docker tag ${BACKEND_IMAGE}:latest ${NEXUS_REPOSITORY}/${BACKEND_IMAGE}:latest"
                        sh "docker push ${NEXUS_REPOSITORY}/${BACKEND_IMAGE}:latest"
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
