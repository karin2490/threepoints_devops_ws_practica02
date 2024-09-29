@Library('threepoints-sharedlib') _

pipeline {
    agent any
    environment {
        WORKSPACE = "${env.WORKSPACE}"
        BRANCH_NAME = "${env.BRANCH_NAME}"
    }
    stages {
        stage('Checkout') {
            steps {
                echo 'Descargando el código desde GitHub...'
                git branch: 'main', url: 'https://github.com/karin2490/threepoints_devops_ws_practica02.git'
            }
        }
        stage('Parallel Steps') {
            parallel {
                stage('Pruebas de SAST') {
                    steps {
                        script {
                            // Llama a la función de análisis de calidad de código
                            sonarAnalysis(abortPipeline: false, qualityGateCheck: true)
                        }
                    }
                }
                stage('Imprimir Variables') {
                    steps {
                        echo "La ruta de WORKSPACE es: ${WORKSPACE}"
                        echo "La rama es: ${env.BRANCH_NAME}"
                    }
                }
            }
        }
        stage('Configurar archivo') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'Credentials_Threepoints', passwordVariable: 'password', usernameVariable: 'user')]) {
                    writeFile file: 'credentials.ini', text: "[credentials]\nuser=${user}\npassword=${password}"
                    archiveArtifacts artifacts: 'credentials.ini', fingerprint: true
                }
            }
        }
        stage('Build') {
            steps {
                echo 'Construyendo el contenedor Docker...'
                bat 'docker build -t devops_ws .'
            }
        }
        stage('Despliegue del servidor') {
            steps {
                script {
                    bat '''
                        docker ps -q -f name=devops >nul 2>&1
                        if %errorlevel%==0 (
                            docker stop devops
                        ) else (
                            echo "El contenedor 'devops' no está en ejecución."
                        )
                    '''
                    bat 'docker run -d -p 8090:8090 --name devops devops_ws'
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline finalizado.'
        }
    }
}
