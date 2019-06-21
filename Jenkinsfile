pipeline {
    agent any
    tools{
        maven 'maven3';
        jdk 'jdk8';
    }
    stages {
        stage('build') {
            steps {
                sh 'mvn clean install'
            }
            post {
                success {
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
        stage('SonarQube analysis') {
            steps {
                withSonarQubeEnv('Sonar') {
                    // requires SonarQube Scanner for Maven 3.2+
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.6.0.1398:sonar -Dsonar.host.url=$SONAR_HOST_URL  -Dsonar.login=27b3ca2c69f2ad10684258ac10b878b71190ac45 -Dsonar.projectKey=mreed-dcdojo -Dsonar.organization=matthewreed26-github'
                }
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('upload nexus') {
            steps {
                sh 'mvn deploy'
            }
        }
    }
}
