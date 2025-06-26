pipeline {
    agent any
    tools {
        maven 'Maven 3.8.1'
        jdk 'JDK11'
    }

    environment {
        NEXUS_URL = "http://your-nexus-url"
        SONAR_SERVER = "http://your-sonarqube-url"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://your-repo-url/java-web-gui-app.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Deploy to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-creds', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    sh 'mvn deploy -Dnexus.username=$NEXUS_USER -Dnexus.password=$NEXUS_PASS'
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'tomcat-creds', usernameVariable: 'TOMCAT_USER', passwordVariable: 'TOMCAT_PASS')]) {
                    sh """
                    curl -T target/java-web-gui-app.war "http://$TOMCAT_USER:$TOMCAT_PASS@your-tomcat-host:8080/manager/text/deploy?path=/java-web-gui-app&update=true"
                    """
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'target/*.war', fingerprint: true
        }
    }
}
