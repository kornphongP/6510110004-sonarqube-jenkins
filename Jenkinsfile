pipeline {
    agent any

    tools {
        maven 'maven'
        jdk 'java'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/master']],
                    extensions: [],
                    userRemoteConfigs: [[
                        credentialsId: 'github access',
                        url: 'https://github.com/sreenivas449/java-hello-world-with-maven.git'
                    ]]
                ])
            }
        }

        stage('Maven Version') {
            steps {
                script {
                    // ตรวจสอบเวอร์ชัน Maven ใน Docker
                    sh 'docker run -i --rm maven:3.9.9 mvn --version'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build project ภายใน Docker container
                    sh '''
                    docker run -i --rm --name my-maven-project \
                      -v "${PWD}:/usr/src/mymaven" -w /usr/src/mymaven \
                      maven:3.9.9 mvn clean install
                    '''
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    sh '''
                    docker run -i --rm --name my-maven-project \
                      -v "${PWD}:/usr/src/mymaven" -w /usr/src/mymaven \
                      maven:3.9.9 mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=std-id-6510110004 \
                        -Dsonar.projectName=std-id-6510110004 \
                        -Dsonar.host.url=http://host.docker.internal:9001 \
                        -Dsonar.token=sqp_ef009b3cb203e1763e95eece645ae3a746477dd8
                    '''
                }
            }
        }
    }
}
