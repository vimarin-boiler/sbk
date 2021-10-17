pipeline {
    agent any

    tools {
        maven 'Maven'
        nodejs 'NodeJs'
    }
  
    stages {
        stage('initial-stage'){
            steps{
                figlet 'Inital'
                
             sh '''
              echo "PATH = ${PATH}"
              echo "M2_HOME = ${M2_HOME}"
              '''
            }
        }

        stage('Compile-Stage'){
            steps{
                figlet 'Compile'
                sh 'mvn clean compile -e'
            }
        }
        
        stage('Test-Stage'){
            steps{
                figlet 'Test'
                sh 'mvn clean test -e'
            }
        }
        
        stage('Dependency-Stage'){
            steps{
                figlet 'Dependency-Check'
                sh 'mvn org.owasp:dependency-check-maven:check'
                
                archiveArtifacts artifacts: 'target/dependency-check-report.html', followSymlinks: false
            }
        }
        
        stage('Sonarqube-Stage'){
           steps{
               figlet 'SonarQube'
               script{
                   def scannerHome = tool 'SonarQube Scanner'
                   
                   withSonarQubeEnv('Sonar Server'){
                       sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=tarea-sonar -Dsonar.sources=. -Dsonar.projectBaseDir=${env.WORKSPACE} -Dsonar.java.binaries=target/classes -Dsonar.exclusions='**/*/test/**/*, **/*/acceptance-test/**/*, **/*.html'"
                   }
               }
           }
          }       


            stage('Slack-Stage'){
                      steps{
                          figlet 'Slack Message'
                          
                            slackSend channel: 'notificacion-jenkins',
                            color: 'danger',
                            message: "Se ha terminado una ejecucion del pipeline."
                      }
                  }

    }
    
      
    
           
}
