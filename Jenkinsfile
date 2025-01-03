pipeline {
    agent any

    environment {
        JAVA_HOME = '/usr/lib/jvm/java-17-openjdk-amd64'
        MAVEN_HOME = '/usr/share/maven'
        PATH = "${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Set up Java 17') {
            steps {
                echo 'Setting up Java 17...'
                sh 'sudo apt update'
                sh 'sudo apt install -y openjdk-17-jdk'
            }
        }

        stage('Set up Maven') {
            steps {
                echo 'Setting up Maven...'
                sh 'sudo apt install -y maven'
            }
        }

        stage('Build with Maven') {
            steps {
                echo 'Building project with Maven...'
                sh 'mvn clean package'
            }
        }

        stage('Upload Artifact') {
            steps {
                echo 'Uploading artifact...'
                archiveArtifacts artifacts: 'target/petclinic-0.0.1-SNAPSHOT.jar', allowEmptyArchive: true
            }
        }

        stage('Run Application') {
            steps {
                echo 'Running Spring Boot application...'
                sh 'nohup mvn spring-boot:run &'
                sleep(time: 15, unit: 'SECONDS') // Wait for the application to fully start

                // Fetch the public IP and display the access URL
                script {
                    def publicIp = sh(script: "curl -s https://checkip.amazonaws.com", returnStdout: true).trim()
                    echo "The application is running and accessible at: http://${publicIp}:8080"
                }
            }
        }
