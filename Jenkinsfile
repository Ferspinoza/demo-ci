pipeline {
  agent any
  options { timestamps() }
  environment { MVN = "mvn" } // o ruta completa si fuese necesario
  stages {
    stage('Build (sin tests)') {
      steps {
        bat '%MVN% -B -DskipTests package'
      }
    }
    stage('Unit Tests') {
      steps {
        bat '%MVN% -B test'
        junit 'target/surefire-reports/*.xml'
      }
    }
    stage('Package') {
      steps {
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
      }
    }
    stage('Deploy local') {
      steps {
        bat 'mkdir "%WORKSPACE%\\deploy\\demo-ci" 2>nul & copy /Y target\\*.jar "%WORKSPACE%\\deploy\\demo-ci\\"'
      }
    }
  }
  post {
    success { echo 'Pipeline OK: jar archivado y copiado a deploy/.' }
    failure { echo 'Algo falló: revisa la etapa en rojo.' }
  }
}
