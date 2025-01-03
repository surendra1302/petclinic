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
                echo 'Checking out the application code...'
                checkout scm
            }
        }

        stage('Setup Environment') {
            steps {
                echo 'Setting up environment with Java 17 and Maven...'
                sh '''
                    sudo apt-get update
                    sudo apt-get install -y openjdk-17-jdk maven
                '''
            }
        }

        stage('Build Application') {
            steps {
                echo 'Building the application using Maven...'
                sh 'mvn clean package'
            }
        }

        stage('Test Application') {
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
        }

        stage('Archive Artifact') {
            steps {
                echo 'Archiving the built JAR artifact...'
                archiveArtifacts artifacts: 'target/petclinic-0.0.1-SNAPSHOT.jar', allowEmptyArchive: false
            }
        }

        stage('Run Application') {
            steps {
                echo 'Starting the Spring Boot application...'
                sh 'nohup java -jar target/petclinic-0.0.1-SNAPSHOT.jar &'
                sleep(time: 20, unit: 'SECONDS') // Wait for the application to start

                // Fetch and display the public IP with access URL
                script {
                    def publicIp = sh(script: "curl -s https://checkip.amazonaws.com", returnStdout: true).trim()
                    echo "The application is running and accessible at: http://${publicIp}:8080"
                }
            }
        }

        stage('Validate App is Running') {
            steps {
                echo 'Validating the application...'
                script {
                    def response = sh(script: 'curl --write-out "%{http_code}" --silent --output /dev/null http://localhost:8080', returnStdout: true).trim()
                    if (response == "200") {
                        echo 'The application is running successfully!'
                    } else {
                        error("The application failed to start. HTTP response code: ${response}")
                    }
                }
            }
        }

      stage('Wait for 5 minutes') {
            steps {
                echo 'Waiting for 5 minutes...'
                sleep(time: 5, unit: 'MINUTES')  // Wait for 5 minutes
            }
        }
    }

    post {
        always {
            echo 'Cleaning up temporary resources...'
            sh 'pkill -f "petclinic-0.0.1-SNAPSHOT.jar" || true'
        }
    }
}
