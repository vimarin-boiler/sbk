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

        
         stage('DAST'){
            steps{
                figlet 'Owasp Zap DAST'
        		
        		script{
        		   env.DOCKER = tool "Docker"
        		   env.DOCKER_EXEC = "${DOCKER}/bin/docker"
        		   env.TARGET = 'http://zero.webappsecurity.com/'
        	
        		    sh '${DOCKER_EXEC} rm -f zap2'
        		    sh "${DOCKER_EXEC} pull owasp/zap2docker-stable"
                    sh '${DOCKER_EXEC} run --add-host="localhost:10.192.10.4" --rm -e LC_ALL=C.UTF-8 -e LANG=C.UTF-8 --name zap2 -u zap -p 8093:8093 -d owasp/zap2docker-stable zap.sh -daemon -port 8093 -host 0.0.0.0 -config api.disablekey=true'
                    sh '${DOCKER_EXEC} run --add-host="localhost:10.192.10.4" -v /var/jenkins_home/tools:/zap/wrk/:rw --rm -i owasp/zap2docker-stable zap-baseline.py -t "http://zero.webappsecurity.com/" -I -r zap_baseline_report.html -l PASS'	
        		   
        		   publishHTML([
        				    allowMissing: false,
        				    alwaysLinkToLastBuild: false,
        				    keepAll: false,
        				    reportDir: '/var/jenkins_home/tools/',
        				    reportFiles: 'zap_baseline_report.html',
        				    reportName: 'HTML Report',
        				    reportTitles: ''])
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
