pipeline {
  agent any
  options { timestamps() }
  environment { MVN = "mvn" } // si falla: C:\\Users\\TIC\\scoop\\shims\\mvn.cmd

  stages {
    stage("Build (sin tests)") {
      steps {
        bat "%MVN% -v"
        bat "%MVN% -B -DskipTests package"
      }
    }
    stage("Unit Tests") {
      steps {
        bat "%MVN% -B test"
        junit "target/surefire-reports/*.xml"
      }
    }
    stage("Package") {
      steps {
        archiveArtifacts artifacts: "target/*.jar", fingerprint: true
      }
    }
    stage("Deploy local") {
      steps {
        bat "mkdir \"%WORKSPACE%\\deploy\" 2>nul & copy /Y target\\*.jar \"%WORKSPACE%\\deploy\\\""
      }
    }
  }

  post {
    success { echo "Pipeline OK: jar archivado y copiado a deploy/." }
    failure { echo "Algo falló: revisa la etapa en rojo." }
  }
}
