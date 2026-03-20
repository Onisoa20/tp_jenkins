pipeline {
    agent any

    tools {
        // IMPORTANT: "maven3" doit être le nom exact que vous avez donné à votre installation Maven dans "Administrer Jenkins -> Tools (ou Global Tool Configuration)"
        maven 'maven3'
    }


    stages {
        stage('Checkout') {
            steps {
                // Contournement : Puisque vous n'utilisez pas "Pipeline script from SCM", on lui donne directement le lien
                git branch: 'main', url: 'https://github.com/Onisoa20/tp_jenkins.git'
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
                // Utilise le settings.xml fraîchement créé qui pointe vers 'nexus-releases' et 'nexus-snapshots' du pom.xml
                withCredentials([usernamePassword(credentialsId: 'nexus-credentials', passwordVariable: 'NEXUS_PWD', usernameVariable: 'NEXUS_USER')]) {
                    sh 'mvn deploy -s settings.xml'
                }
            }
        }
    }
}
