pipeline {
  agent any
  options { timestamps() }
  stages {
    stage('Build (sin tests)') {
      steps {
        bat 'mvn -B -DskipTests clean package'
      }
    }
    stage('Unit Tests') {
      steps {
        bat 'mvn -B test'
        junit 'target/surefire-reports/*.xml'
      }
    }
    stage('Run app') {
      steps {
        bat 'java -cp target\\demo-ci-1.0.0-SNAPSHOT.jar com.retailoff.App'
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
