pipeline {
    agent any
    tools {
        nodejs 'nodejs' // Configure Node.js dans Jenkins
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

        stage("Run Docker Compose") {
            steps {
                dir("Projet-DevOps") {
                    sh "docker-compose up -d"
                }
            }
        }
    }
}
