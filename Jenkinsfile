pipeline {
  agent any 
  tools {
    maven 'Maven'
  }
  stages {
    
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }
    
    stage ('Check-Git-Secrets') {
      steps {
        sh 'whoami'
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/rajesh1274/DevSecOps.git > trufflehog'
        sh 'cat trufflehog'
      }
    }
    
    
    stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/rajesh1274/DevSecOps/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'  
      }
    }
    
    stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar'
          sh 'cat target/sonar/report-task.txt'
        }
      }
    }
    
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    
    stage ('Deploy-To-Tomcat') {
        steps {
            
                sh 'sudo cp target/*.war /home/rajesh4debug/prod/apache-tomcat-8.5.42/webapps/webapp.war'
                  
        }       
    }
    
    stage ('DAST') {
      steps {
        
         sh 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://35.188.86.170:8088/webapp/  || true'
        
      }
    }
    
  }
}
