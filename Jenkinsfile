pipeline {
    agent any
    stages {
        stage ('Build Backend') {
            steps {
                bat 'mvn clean package -DskipTests=true'
            }
        }
          stage ('Unit Tests') {
            steps {
                bat 'mvn test'
            }
        }
           stage ('Sonar Analysis') {
               environment {
                   scannerHome = tool 'SONAR_SCANNER'
               }
            steps {
                withSonarQubeEnv('SONAR_LOCAL') {
                     bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=a72021efc6e0e146140df78607e4ff75c1b3c723 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"  
                }
            }
        }
            stage ('Quality Gate') {
                    steps {
                      sleep(5)
                      timeout(time: 1, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                }  
            }
        }
            stage ('Deploy Backend') {
                    steps {
                        deploy adapters: [tomcat8(credentialsId: 'TamcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
                    }
            }
            stage ('API Test') {
                    steps {
                        dir('api-test') {
                            git credentialsId: 'github_login', url: 'https://github.com/rafaelsilva9126/tasks-api-test'
                            bat 'mvn test'
                        }
                    }
            }
    }
}


