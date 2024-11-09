pipeline {
    agent {
        label "jenkins-agent"
    }

    tools {
        jdk "Java21"
        maven "Maven3"
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
    }
}