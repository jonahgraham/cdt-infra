pipeline {
  agent {
    kubernetes {
      label 'cdt-bigmem-agent-pod'
      yamlFile 'jenkins/pod-templates/cdt-bigmem-pod-standard.yaml'
    }
  }
  options {
    timestamps()
  }
  stages {
    stage('Run build') {
      steps {
        container('cdt') {
          git branch: 'master', url: 'git://git.eclipse.org/gitroot/cdt/org.eclipse.cdt.git'
          timeout(activity: true, time: 60) {
            withEnv(['MAVEN_OPTS=-Xmx3g -Xms768m']) {
                sh "/usr/share/maven/bin/mvn clean package javadoc:aggregate -Pbuild-standalone-debugger-rcp \
-Ddsf.gdb.tests.timeout.multiplier=50 -Dindexer.timeout=300 \
-Dmaven.repo.local=/home/jenkins/.m2/repository --settings /home/jenkins/.m2/settings.xml"
            }

            archiveJavadoc 'target/site/apidocs'
          }
        }
      }
    }
  }
}
