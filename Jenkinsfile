pipeline {
    agent any
    stages {
        stage ('Checkout') {
          steps {
            git 'https://github.com/effectivejenkins/spring-petclinic.git'
          }
        }
        stage('Build') {
            agent { docker 'maven:3.5-alpine' }
            steps {
                sh 'mvn clean package'
                junit '**/target/surefire-reports/TEST-*.xml'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
        stage('Deploy') {
          steps {
            input 'Do you approve the deployment?'
            withCredentials([sshUserPrivateKey(credentialsId: 'jenkins-slave-01', keyFileVariable: 'slavenodeaccess', passphraseVariable: '', usernameVariable: 'ec2-user')]) {
            sh 'scp target/*.jar ec2-user@172.31.31.47:/opt/pet/'
            sh "ssh ec2-user@172.31.31.47 'nohup java -jar /opt/pet/spring-petclinic-1.5.1.jar &'"
            }
          }
        }
    }
}
