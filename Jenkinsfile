pipeline {
    agent any

    tools {
        jdk 'jdk17'
        nodejs 'nodejs'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/nasiroddin-khatib/project'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectName=2048-game \
                    -Dsonar.projectKey=2048-game
                    '''
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Trivy File Scan') {
            steps {
                sh 'trivy fs .'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t 2048-game .'
            }
        }

        stage('Docker Tag') {
            steps {
                sh 'docker tag 2048-game nasir590/2048-game:latest'
            }
        }

        stage('Docker Push') {
            steps {
                withDockerRegistry(credentialsId: 'docker') {
                    sh 'docker push nasir590/2048-game:latest'
                }
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh 'trivy image nasir590/2048-game:latest'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
}
