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
                dir("Projet-DevOps/react-crud-web-api-master") {
                    sh "npm install"
                    sh "npm run build"
                    sh "docker build -t react-frontend:latest ."
                }
            }
        }

        stage("Build Backend Image") {
            steps {
                dir("Projet-DevOps/nodejs-express-sequelize-mysql-master") {
                    sh "npm install"
                    sh "docker build -t node-backend:latest ."
                }
            }
        }

        stage("SonarQube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') { // 'sonar-server' doit correspondre au nom dans la config Jenkins
                    sh '/opt/sonar-scanner/sonar-scanner-6.2.1.4610-linux-x64/bin/sonar-scanner \
                        
                        -Dsonar.projectKey=Projet-DevOps \
                        -Dsonar.sources=Projet-DevOps \
                        -Dsonar.host.url=http://192.168.1.20:9000 \
                        -Dsonar.login=sonar-token
                    """
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

    post {
        always {
            script {
                // Attendre le résultat du Quality Gate de SonarQube
                waitForQualityGate abortPipeline: true
            }
        }
    }
}
