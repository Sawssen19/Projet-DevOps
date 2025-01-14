pipeline {
    agent any

    tools {
        nodejs 'nodejs' // Configure Node.js dans Jenkins
    }

    environment {
        SONARQUBE = 'sonarqube'  // Nom de l'installation SonarQube configurée dans Jenkins
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

        stage("Install Dependencies and Run Tests") {
            steps {
                dir("Projet-DevOps/react-crud-web-api-master") { // React - Frontend
                    sh "npm install"
                    sh "npm test -- --coverage"  // Exécuter les tests pour générer les rapports de couverture
                }
                dir("Projet-DevOps/nodejs-express-sequelize-mysql-master") { // Node.js - Backend
                    sh "npm install"
                    sh "npm test -- --coverage"  // Exécuter les tests pour générer les rapports de couverture
                }
            }
        }

        stage("SonarQube Analysis") {
            steps {
                script {
                    // Exécuter l'analyse SonarQube sans fichier sonar-project.properties
                    withSonarQubeEnv(SONARQUBE) { // Assurez-vous que SonarQube est correctement configuré dans Jenkins
                        dir("Projet-DevOps/react-crud-web-api-master") {
                            sh "sonar-scanner -Dsonar.projectKey=frontend-react -Dsonar.sources=src -Dsonar.tests=src -Dsonar.test.inclusions=**/*.test.js -Dsonar.javascript.lcov.reportPaths=coverage/lcov-report/index.js"
                        }
                        dir("Projet-DevOps/nodejs-express-sequelize-mysql-master") {
                            sh "sonar-scanner -Dsonar.projectKey=backend-nodejs -Dsonar.sources=src -Dsonar.tests=src -Dsonar.test.inclusions=**/*.test.js -Dsonar.javascript.lcov.reportPaths=coverage/lcov-report/index.js"
                        }
                    }
                }
            }
        }

        stage("Build Frontend Image") {
            steps {
                dir("Projet-DevOps/react-crud-web-api-master") { // Chemin ajusté pour le frontend
                    sh "docker build -t ${FRONTEND_IMAGE}:latest ."
                }
            }
        }

        stage("Build Backend Image") {
            steps {
                dir("Projet-DevOps/nodejs-express-sequelize-mysql-master") { // Chemin ajusté pour le backend
                    sh "docker build -t ${BACKEND_IMAGE}:latest ."
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
