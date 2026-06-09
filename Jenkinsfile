pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/mdmbest/microservices-demo.git'
            }
        }

        stage('Scan - Trivy') {
            steps {
                bat '''
                    docker run --rm -v "%CD%:/project" aquasec/trivy:latest fs /project --format json --output trivy-report.json --scanners vuln,secret,misconfig || exit 0
                '''
            }
            post {
                always {
                    archiveArtifacts artifacts: 'trivy-report.json', allowEmptyArchive: true
                }
            }
        }
    }

    post {
        always {
            emailext(
                subject: "microservices-demo Build #${BUILD_NUMBER} - ${currentBuild.currentResult}",
                body: """
                    <h2>Rapport DevSecOps - microservices-demo</h2>
                    <p><b>Statut :</b> ${currentBuild.currentResult}</p>
                    <p><b>Build :</b> #${BUILD_NUMBER}</p>
                    <p><b>Duree :</b> ${currentBuild.durationString}</p>
                    <p>Rapport Trivy en piece jointe</p>
                    <a href="${BUILD_URL}">Voir le build Jenkins</a>
                """,
                mimeType: 'text/html',
                attachmentsPattern: 'trivy-report.json',
                to: 'mamediarram664@gmail.com'
            )
        }
        failure {
            emailext(
                subject: "ERREURS - microservices-demo Build #${BUILD_NUMBER}",
                body: """
                    <h2>Erreurs detectees !</h2>
                    <p>Le build #${BUILD_NUMBER} a echoue.</p>
                    <a href="${BUILD_URL}console">Voir les logs</a>
                """,
                mimeType: 'text/html',
                to: 'mamediarram664@gmail.com'
            )
        }
    }
}
