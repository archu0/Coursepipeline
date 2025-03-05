pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment {
        DOCKER_URL = 'archu09/workerpipeline'

    }
    stages {
        stage("Pull SRC") {
            steps {
                git 'https://github.com/archu0/Coursepipeline.git'
            }
        }
        stage("Prepare Build") {
            steps {
                sh 'mvn clean package'
            }
        }
        stage("Copy *.war file to ansible") {
            steps {
                sh 'mv target/Course.war .'
                sshPublisher(
                    continueOnError: false, 
                    failOnError: true,
                    publishers: [
                        sshPublisherDesc(
                            configName: "marcos",
                            transfers: [sshTransfer(sourceFiles: 'Course.war')],
                            verbose: true
                        )
                    ]
                )
            }
        }
        stage("Copy Docker file to ansible") {
            steps {
                sshPublisher(
                    continueOnError: false, 
                    failOnError: true,
                    publishers: [
                        sshPublisherDesc(
                            configName: "marcos",
                            transfers: [
                                sshTransfer(sourceFiles: 'Dockerfile'),
                                sshTransfer(execCommand: "docker rm -f tomcat; docker rmi marcos; docker build -t marcos .")
                            ],
                            verbose: true
                        )
                    ]
                )
            }
        }
         
        stage('provide tag') {
            steps {
                script {
                    sh "docker tag marcos $DOCKER_URL"
                }
            }
        }
        stage('Login to Docker Hub') {
            steps {
                        sh "docker login -u archu09 -p Archana09* "
                    }
            }
    
        
        stage('Push Docker Image') {
            steps {
                    sh "docker push $DOCKER_URL"  
            }
        }
        }
}
