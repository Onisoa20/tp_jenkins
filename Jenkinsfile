pipeline {
    agent any

    tools {
        // Le nom de l'installation Maven configurée dans Jenkins est "Maven"
        maven 'Maven'
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
                // Compilation avec le settings.xml pour les accès
                sh 'mvn clean install -s settings.xml'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // 'pipeline' doit être le NOM du serveur dans Administrer Jenkins > System
                withSonarQubeEnv('pipeline') {
                    sh 'mvn sonar:sonar -s settings.xml'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-credentials', passwordVariable: 'NEXUS_PWD', usernameVariable: 'NEXUS_USER')]) {
                    sh 'mvn deploy -s settings.xml'
                }
            }
        }
    }
}
