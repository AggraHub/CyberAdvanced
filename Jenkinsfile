pipeline {
    environment {
        REPOSITORY = "cyberadvanced"
        GPG_PUBLIC_KEY = credentials('GPGkey')
    }
    agent any
    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        stage('gpg sign') {
            steps {
                sh 'gpg --armor --detach-sign -u ${GPG_PUBLIC_KEY} ./Dockerfile'
            }
        }
        stage('Build image and tag with build number') {
            steps {
                script {
                    sh 'docker build -t ${REPOSITORY}:${BUILD_NUMBER} .'
                }
            }
        }
        stage('verify signature') {
            steps {
                sh "gpg --verify ./Dockerfile.asc"
            }
        }
        stage('Generate SBOM with syft') {
            steps {
                script {
                    sh 'syft -o json ${REPOSITORY}:${BUILD_NUMBER} > sbom-${BUILD_NUMBER}.json'
                }
            }
        }
        stage('Generate vulnerability listing with grype') {
            steps {
                script {
                    sh 'grype sbom:sbom-${BUILD_NUMBER}.json'
                    sh 'grype -o json sbom:sbom-${BUILD_NUMBER}.json > vulns-${BUILD_NUMBER}.json'
                }
            }
        }
        stage('Clean up') {
            steps {
                archiveArtifacts '*.json'
                sh '''
                    docker rmi ${REPOSITORY}:${BUILD_NUMBER}
                    rm *.json
                '''
            }
        }
    }
}
