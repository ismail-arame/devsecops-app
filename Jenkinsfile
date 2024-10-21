pipeline {
  agent any
  tools {
    maven 'Maven'
  }
  stages{
    stage ('Initialize'){
      steps{
        sh '''
          echo "PATH = ${PATH}"
          echo "M2_HOME = ${M2_HOME}"
          '''
      }
    }
    stage ('Check-Git-Secrets'){
      steps{
        sh 'rm output || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/ismail-arame/devsecops-app.git > output' 
        sh 'cat output'
      }
    }
    stage ('Source-Composition-Analysis'){
      steps{
        sh 'rm owasp* || true'
        sh 'wget "https://raw.githubusercontent.com/ismail-arame/devsecops-app/refs/heads/main/owasp-dependency-check.sh" '
        sh 'chmod +x owasp-dependency-check.sh'
        sh 'bash owasp-dependency-check.sh'
        sh 'cat ${WORKSPACE}/odc-reports/dependency-check-report.xml'
      }
    }
    stage ('Build'){
      steps{
        sh 'mvn clean package'
      }
    }
    stage ('Deploy-To-Tomcat') {
      steps{
        sshagent(['tomcat']) {
          sh 'scp -o StrictHostKeyChecking=no target/*.war root@165.232.88.249:/root/prod/apache-tomcat-9.0.96/webapps/webapp.war'
        }
      }
    }
  }
}
