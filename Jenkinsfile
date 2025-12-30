@Library("Shared") _

pipeline {
    agent { label "zero" }

    environment {
        IMAGE_NAME = "flask"
        COMPOSE_FILE = "docker-compose.yml"
        IMAGE_TAG = "latest"
        DOCKER_USER = "ashishwayachal12"
    }

    stages {
        stage("Hello") {
            steps {
                hello()
            }
        }

        stage("Code") {
            steps {
                script {
                    clone("https://github.com/Ashishwayachal12/flask-app-Docker.git", "main")
                }
                sh "ls -l ${COMPOSE_FILE} || echo '⚠ Compose file missing!'"
            }
        }

        stage("Build") {
            steps {
                script {
                    docker_build("flask-app", IMAGE_TAG, DOCKER_USER)
                }
            }
        }

        stage("Push to DockerHub") {
            steps {
                script {
                    docker_push("flask-app", IMAGE_TAG, DOCKER_USER)
                }
            }
        }
        stage("Deploy") {
            steps {
                echo "Deploying container using Docker Compose..."
                sh """
                    docker rm -f ${IMAGE_NAME} 2>/dev/null || true
                    docker compose -f ${COMPOSE_FILE} up -d --build
                    docker ps -a
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully! Image pushed & container deployed."
        }
        failure {
            echo "❌ Pipeline failed — check logs above."
        }
    }
}
