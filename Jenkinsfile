pipeline {
  agent any
  options { timestamps() }
  environment { MVN = "mvn" } // si falla: C:\\Users\\TIC\\scoop\\shims\\mvn.cmd
  stages {
    stage("Bootstrap proyecto") {
      steps {
        echo "Creando proyecto Maven mínimo en el workspace..."
        bat '''
          if not exist "demo-ci" mkdir "demo-ci"
          if not exist "demo-ci\\src" mkdir "demo-ci\\src"
          if not exist "demo-ci\\src\\main\\java\\com\\retailoff" mkdir "demo-ci\\src\\main\\java\\com\\retailoff"
          if not exist "demo-ci\\src\\test\\java\\com\\retailoff"  mkdir "demo-ci\\src\\test\\java\\com\\retailoff"
        '''
        dir("demo-ci") {
          writeFile file: "pom.xml", text: "<project xmlns=\\"http://maven.apache.org/POM/4.0.0\\" xmlns:xsi=\\"http://www.w3.org/2001/XMLSchema-instance\\" xsi:schemaLocation=\\"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd\\"><modelVersion>4.0.0</modelVersion><groupId>com.retailoff</groupId><artifactId>demo-ci</artifactId><version>1.0.0-SNAPSHOT</version><properties><maven.compiler.source>17</maven.compiler.source><maven.compiler.target>17</maven.compiler.target><project.build.sourceEncoding>UTF-8</project.build.sourceEncoding></properties><dependencies><dependency><groupId>junit</groupId><artifactId>junit</artifactId><version>4.13.2</version><scope>test</scope></dependency></dependencies><build><plugins><plugin><groupId>org.apache.maven.plugins</groupId><artifactId>maven-surefire-plugin</artifactId><version>3.2.5</version></plugin></plugins></build></project>"
          writeFile file: "src/main/java/com/retailoff/App.java", text: "package com.retailoff; public class App { public static void main(String[] args){ System.out.println(\\"Hola Retail Off - CI listo!\\"); }}"
          writeFile file: "src/test/java/com/retailoff/AppTest.java", text: "package com.retailoff; import static org.junit.Assert.*; import org.junit.Test; public class AppTest { @Test public void testApp(){ assertTrue(true); }}"
        }
      }
    }
    stage("Build (sin tests)") { steps { dir("demo-ci"){ bat "%MVN% -v" ; bat "%MVN% -B -DskipTests package" } } }
    stage("Unit Tests")       { steps { dir("demo-ci"){ bat "%MVN% -B test" ; junit "target/surefire-reports/*.xml" } } }
    stage("Package")          { steps { archiveArtifacts artifacts: "demo-ci/target/*.jar", fingerprint: true } }
    stage("Deploy local")     { steps { bat "mkdir \\"%WORKSPACE%\\deploy\\demo-ci\\" 2>nul & copy /Y demo-ci\\target\\*.jar \\"%WORKSPACE%\\deploy\\demo-ci\\\\"" } }
  }
  post {
    success { echo "Pipeline OK: jar archivado y copiado a deploy/." }
    failure { echo "Algo falló: revisa la etapa en rojo." }
  }
}
