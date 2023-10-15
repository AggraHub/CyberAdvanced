pipeline {
    agent any
    stages {
        stage("Generate Software Bill of Materials (sbom) with Syft") {
            steps {
                sh '''
                    curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s -- -b /usr/local/bin
                    syft app:${BUILD_NUMBER} --scope all-layers -o json > sbom-${BUILD_NUMBER}.json
                    syft app:${BUILD_NUMBER} --scope all-layers -o table > sbom-${BUILD_NUMBER}.txt
                '''
            }
        }
    }
}
