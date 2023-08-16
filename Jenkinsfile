pipeline {

    agent any

    environment {
        BRANCH_NAME = "${env.gitlabSourceBranch}"
    }

    stages {

        stage('Checkout') {
            steps {
                script {
                    checkout scm
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Build'
                sh "mvn --batch-mode package" 
            }
        }

        stage('Archive Unit Tests Results') {
            steps {
                echo 'Archive Unit Test Results'
               step([$class: 'JUnitResultArchiver', testResults: 'target/surefire-reports/TEST-*.xml'])
            }
        }
        
        stage('Publish Unit Test results report') {
            steps {
                echo 'Report'
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: true, keepAll: false, reportDir: 'target/site/jacoco/', reportFiles: 'index.html', reportName: 'jacaco report', reportTitles: ''])

             }
        }
        
        stage('SonarQube Analysis') {
            tools {
               jdk 'JDK 11.0.1'
            }
            steps {
                withSonarQubeEnv('SonarQube 6 Community') {
                  script {
                    sh "mvn sonar:sonar \
  -Dsonar.projectKey=TechLabs-TLABS \
  -Dsonar.host.url=https://umane.everis.com/sonarqubece \
  -Dsonar.login=91aea770e4896a2581c6780666ca27a1e009f4c5 \
  -Dsonar.branch.name=$BRANCH_NAME"
                    }
                }
            }
        }
    }
}

