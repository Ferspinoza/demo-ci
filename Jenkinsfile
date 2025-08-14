pipeline {
  agent any
  options { timestamps() }
  triggers { pollSCM('H/5 * * * *') } // busca cambios ~cada 5 min
  environment { MVN = "mvn" }

  stages {
    stage("Build (sin tests)") {
      steps {
        bat 'mvn -B -DskipTests clean package'
      }
    }
    stage("Unit Tests") {
      steps {
        bat 'mvn -B test'
        junit 'target/surefire-reports/*.xml'
      }
    }
    stage("Package") {
      steps {
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
      }
    }
    stage("Run app") {
      steps {
        bat 'java -jar target\\demo-ci-1.0.0-SNAPSHOT.jar'
      }
    }
    stage("Deploy local") {
      steps {
        bat 'mkdir "%WORKSPACE%\\deploy\\demo-ci" 2>nul & copy /Y target\\*.jar "%WORKSPACE%\\deploy\\demo-ci\\"'
      }
    }
  }

  post {
    success { echo "Pipeline OK: jar archivado, probado, ejecutado y copiado a deploy/." }
    failure { echo "Algo fall?: revisa la etapa en rojo." }
  }
}
