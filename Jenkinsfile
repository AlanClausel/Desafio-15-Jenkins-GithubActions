pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        RepositoryDockerHub = 'alanclausel'
        NameContainer = "portfolio-website"
    }
    stages {
        stage('Install Hadolint') {
            steps {
                sh 'mkdir -p ~/bin'
                sh 'wget https://github.com/hadolint/hadolint/releases/download/v2.7.0/hadolint-Linux-x86_64 -O ~/bin/hadolint'
                sh 'chmod +x ~/bin/hadolint'
            }
        }

        stage('Lint Dockerfile') {
            steps {
                script {
                    def hadolintExitCode = sh(script: "~/bin/hadolint docker/dockerfile", returnStatus: true)
                    if (hadolintExitCode != 0) {
                        currentBuild.result = 'FAILURE'
                        error("Hadolint found issues in the Dockerfile")
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh "docker build -t ${env.RepositoryDockerHub}/${env.NameContainer}:${env.BUILD_NUMBER} docker/."
            }
        }

        stage('Login to Dockerhub') {
            steps {
                sh "echo \$DOCKERHUB_CREDENTIALS_PSW | docker login -u \$DOCKERHUB_CREDENTIALS_USR --password-stdin"
            }
        }

        stage('Push image to Dockerhub') {
            steps {
                sh "docker push ${env.RepositoryDockerHub}/${env.NameContainer}:${env.BUILD_NUMBER}"
            }
        }

        stage('Deploy container') {
            steps {
                sh "docker stop ${env.NameContainer} || true"
                sh "docker rm -f ${env.NameContainer} || true"
                sh "docker run -d -p 8082:80 --name ${env.NameContainer} ${env.RepositoryDockerHub}/${env.NameContainer}:${env.BUILD_NUMBER}"
            }
        }

        stage('Docker logout') {
            steps {
                sh "docker logout"
            }
        }
    }
}
