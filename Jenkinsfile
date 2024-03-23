pipeline {
    agent any
    tools {
	    maven "Maven3"
	    jdk "JDK21"
	}
    environment {
        SONAR_TOKEN = credentials('squ_3f1773f3ba2c7e33d6c3f971267fe8866b9bc706')
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/taltongsreng/sonar-demo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo "Now Archiving."
                    archiveArtifacts artifacts: '**/*.jar'
                }
            }
        }
        stage('Test'){
            steps {
                sh 'mvn test'
            }

        }

        stage('Checkstyle Analysis'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'sonar5.0'
            }
            steps {
                script {
                    withSonarQubeEnv('sonar') {
                        sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=sonarDemo \
                        -Dsonar.projectName=sonarDemo \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sources=src/ \
                        -Dsonar.java.binaries=target/test-classes/gic/i4/ \
                        -Dsonar.junit.reportsPath=target/surefire-reports/ \
                        -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                        -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                    }
                }
            }
        }
    }
    
    // post {
    //     always {
    //         // Publish SonarQube analysis results
    //         publishAlways: true
    //         timeout(time: 1, unit: 'HOURS') {
    //             waitForQualityGate abortPipeline: true
    //         }
    //     }
    // }
}