pipeline {
    agent any

    environment {
        WORK_DIR = "/var/jenkins_home/${BUILD_ID}"
        MINIKUBE_WORK_DIR = "/home/user/prod"
    }


    stages {
        stage('Checkout') {
            steps {
                echo 'Checkout SCM Jobs Project'
                dir("${WORK_DIR}") {
                    git branch: "main",
                    url: "https://github.com/nicolasrey9/libros_fork.git" // Repositorio de la APP
                }
            }
        }

        stage('Maven Test') {
            steps {
                echo 'Runnning Mvn Test'
                dir("${WORK_DIR}"){
                    sh 'mvn test'  // Ejecuta los tests de Maven
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube Analysis'
                script {
                    def scannerHome = tool 'SonarQube_6.2.1'
                    
                    withSonarQubeEnv('SonarQube') {
                    sh "${scannerHome}/bin/sonar-scanner \
                    -Dsonar.projectBaseDir=${WORK_DIR} \
                    -Dsonar.projectKey=libros \
                    -Dsonar.sources=src/main \
                    -Dsonar.tests=src/test \
                    -Dsonar.java.binaries=target/classes"
            }
                }
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker Image'
            }
        }

        stage('Docker Push') {
            steps {
                echo 'Pushing Docker Image'
            }
        }

        stage('Restart Deployment') {
            steps {
                echo 'Restarting Deplyment'
            }
        }
    }
}