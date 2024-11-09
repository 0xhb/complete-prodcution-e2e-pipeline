pipeline {
    agent {
        label "jenkins-agent"
    }

    tools {
        jdk "Java21"
        maven "Maven3"
    }

    environment {
        APP_NAME = "e2e_Pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "boustta"
        DOCKER_PASS  = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD}"
    }

    stages{
        stage("Cleanup Workspace") {
            steps {
                echo "Hello world"
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/0xhb/complete-prodcution-e2e-pipeline.git'
            }
        }
        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }
        }

        stage("Test Application") {
            steps {
                sh "mvn test"
            }
        }

        stage("SonarQube Scanning") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonar-token')  {
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }

        stage("Quality Gate") {
            steps {
                waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
            }
        }

        stage("Build and Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }
    }
}