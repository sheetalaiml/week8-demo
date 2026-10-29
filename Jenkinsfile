pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Maven Project') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar',
                                 fingerprint: true
            }
        }

        stage('Prepare Deployment') {
            steps {
                powershell '''
                    New-Item -ItemType Directory -Force `
                        -Path "C:\\week8-deploy\\artifact"

                    Copy-Item `
                        "$env:WORKSPACE\\target\\week8-demo-1.0-SNAPSHOT.jar" `
                        "C:\\week8-deploy\\artifact\\week8-demo-1.0-SNAPSHOT.jar" `
                        -Force
                '''
            }
        }

        stage('Deploy with Ansible') {
            steps {
                bat 'wsl.exe -d Ubuntu -- ansible-playbook -i /mnt/c/week8-deploy/ansible/inventory /mnt/c/week8-deploy/ansible/deploy.yml'
            }
        }
    }

    post {
        success {
            echo 'Build and Ansible deployment completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check the stage logs for errors.'
        }
    }
}
