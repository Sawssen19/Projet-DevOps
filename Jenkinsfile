pipeline {
    agent any
    tools {
        nodejs 'nodejs' // Configure Node.js dans Jenkins
    }

        environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "192.168.1.20:8081" // Adresse et port de Nexus
        NEXUS_REPOSITORY = "docker-hosted" // Nom du repository Nexus
        NEXUS_CREDENTIAL_ID = "nexus-user-credentials" // ID des credentials Nexus
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
                    sh "docker build -t react-frontend:latest ."
                }
            }
        }

        stage("Build Backend Image") {
            steps {
                dir("Projet-DevOps/nodejs-express-sequelize-mysql-master") { // Chemin ajusté
                    sh "npm install"
                    sh "docker build -t node-backend:latest ."
                }
            }
        }

                stage("Push Frontend Image to Nexus") {
            steps {
                script {
                    docker.withRegistry("http://${NEXUS_URL}", "${NEXUS_CREDENTIAL_ID}") {
                        sh "docker tag react-frontend:latest ${NEXUS_URL}/${NEXUS_REPOSITORY}/react-frontend:latest"
                        sh "docker push ${NEXUS_URL}/${NEXUS_REPOSITORY}/react-frontend:latest"
                    }
                }
            }
        }

        stage("Push Backend Image to Nexus") {
            steps {
                script {
                    docker.withRegistry("http://${NEXUS_URL}", "${NEXUS_CREDENTIAL_ID}") {
                        sh "docker tag node-backend:latest ${NEXUS_URL}/${NEXUS_REPOSITORY}/node-backend:latest"
                        sh "docker push ${NEXUS_URL}/${NEXUS_REPOSITORY}/node-backend:latest"
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

