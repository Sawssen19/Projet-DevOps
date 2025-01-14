pipeline {
    agent any

    tools {
        nodejs 'nodejs' // Configure Node.js dans Jenkins
    }

   

    stages {
        stage("Clean up") {
            steps {
                deleteDir()  // Nettoie le workspace avant de commencer
            }
        }

        stage("Clone repo") {
            steps {
                sh "git clone https://github.com/Sawssen19/Projet-DevOps.git"
            }
        }
        

        stage("Build Frontend Image") {
            steps {
                dir("Projet-DevOps/react-crud-web-api-master") { // Chemin ajusté pour le frontend
                    sh "npm install"
                    sh "npm run build"
                    sh "docker build -t react-frontend:latest ."
                }
            }
        }

        stage("Build Backend Image") {
            steps {
                dir("Projet-DevOps/nodejs-express-sequelize-mysql-master") { // Chemin ajusté pour le backend
                    sh "npm install"
                    sh "docker build -t node-backend:latest ."
                }
            }
        }

        stage("Run Docker Compose") {
            steps {
                dir("Projet-DevOps") {
                    sh "docker-compose up -d"  // Lancer Docker Compose pour déployer
                }
            }
        }
    }

    post {
        always {
            script {
                // Attendre le résultat du Quality Gate de SonarQube
                waitForQualityGate abortPipeline: true
            }
        }
    }
}
