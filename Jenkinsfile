pipeline {
    agent any


    stages {
        stage('Checkout') {
            steps {
                // Checkout du code (si vous utilisez Git)
                checkout scm
            }
        }

        stage('Build & Unit Tests') {
            steps {
                // Compilation du projet Spring Boot
                sh 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // IMPORTANT: L'installation SonarQube dans Jenkins est configurée sous le nom "pipeline"
                withSonarQubeEnv('pipeline') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                // Attend le résultat de SonarQube et arrête le build s'il y a trop de bugs
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy to Nexus') {
            steps {
                // Déploiement de l'artefact sur Nexus
                // Soit vous avez configuré le settings.xml dans "Managed files" dans Jenkins, soit vous passez le user/pwd
                withCredentials([usernamePassword(credentialsId: 'nexus-credentials', passwordVariable: 'NEXUS_PWD', usernameVariable: 'NEXUS_USER')]) {
                    sh 'mvn deploy -DaltDeploymentRepository=nexus::default::http://localhost:8081/repository/maven-releases/ -s settings.xml'
                }
            }
        }
    }
}
