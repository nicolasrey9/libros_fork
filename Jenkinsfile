pipeline {
    agent any

    environment {
        WORK_DIR = "/var/jenkins_home/${BUILD_ID}"
        MINIKUBE_WORK_DIR = "/home/user/prod"
        WORKDIR_VM2 = "/home/user/prod"
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

        stage('Checkout en VM2') {
            agent {label 'minikube'}
            steps {
                echo 'Checkout SCM Jobs Project'
                dir("${WORKDIR_VM2}/tp") {
                    git branch: "main",
                    url: "https://github.com/nicolasrey9/libros_fork.git" // Repositorio de la APP
                }
            }
        }

        stage('Docker Build') {
            agent {label 'minikube'}
            steps {
                echo 'Building Docker Image'
                dir("${WORKDIR_VM2}/tp") {
                    sh 'docker build -f DockerfileRender -t nicolasrey9/libros-tp .'
                }
            }
        }

        stage('Docker Push') {
            agent {label 'minikube'}
            steps {
                echo 'Pushing Docker Image'
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD' // Usando las credenciales
                    sh 'docker tag nicolasrey9/libros-tp nicolasrey9/libros-tp:latest'
                    sh 'docker push nicolasrey9/libros-tp:latest'
                }
            }
        }

        stage('Restart Deployment') {
            agent {label 'minikube'}
            steps {
                echo 'Restarting Deplyment'
            }
        }
    }
}