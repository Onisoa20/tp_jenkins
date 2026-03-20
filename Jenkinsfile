pipeline {
    agent any

    tools {
        // Optionnel : remplacez par le nom de l'installation Maven et JDK configurées dans Jenkins (Global Tool Configuration)
        // maven 'maven3'
        // jdk 'jdk21'
    }

    environment {
        // Assurez-vous d'avoir créé deux credentials dans Jenkins de type "Secret text" (SonarQube) et "Username/Password" (Nexus)
        SONAR_SCANNER_HOME = tool 'sonar-scanner' // Nom de l'outil SonarScanner dans Jenkins
    }

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
                // IMPORTANT: Remplacez 'sonar' par le nom du serveur SonarQube configuré dans "Manage Jenkins -> System"
                // Pour SonarQube, utilisez un Token d'authentification dans Jenkins plutôt que le mot de passe "Christinah1."
                withSonarQubeEnv('sonar') {
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
