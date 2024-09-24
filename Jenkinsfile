@Library('threepoints-sharedlib') _

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/karin2490/threepoints_devops_ws_practica02.git'
            }
        }

        stage('Compilación') {
            steps {
                echo "Realizando la compilación del proyecto."
            }
        }

        stage('Análisis estático de código') {
            steps {
                // Llamada a la librería compartida
                staticAnalysis(abortPipeline: true)
            }
        }

        stage('Build') {
            steps {
                echo "Construyendo la imagen Docker."
            }
        }
    }

    post {
        always {
            echo 'Pipeline finalizado.'
        }
    }
}
