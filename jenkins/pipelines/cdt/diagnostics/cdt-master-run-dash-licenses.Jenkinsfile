pipeline {
  agent {
    kubernetes {
      yamlFile 'jenkins/pod-templates/cdt-platform-sdk.yaml'
    }
  }
  options {
    timestamps()
    disableConcurrentBuilds()
  }
  stages {
    stage('Git Clone') {
      steps {
        container('cdt') {
          timeout(activity: true, time: 20) {
            /* Running the git fetch command manually is a workaround. See https://bugs.eclipse.org/bugs/show_bug.cgi?id=560283#c16  */
            sh 'git config remote.origin.url https://git.eclipse.org/r/cdt/org.eclipse.cdt.git'
            sh 'git fetch --no-tags --force --progress -- https://git.eclipse.org/r/cdt/org.eclipse.cdt.git +refs/heads/*:refs/remotes/origin/*'
          }
          git branch: 'master', url: 'https://git.eclipse.org/r/cdt/org.eclipse.cdt.git'
        }
      }
    }
    stage('Run Dash Licenses Check') {
      steps {
        container('cdt') {
          timeout(activity: true, time: 20) {
            sh 'MVN="/usr/share/maven/bin/mvn -Dmaven.repo.local=/home/jenkins/.m2/repository \
                      --settings /home/jenkins/.m2/settings.xml" ./releng/scripts/run_dash_licenses.sh'
          }
        }
      }
    }
  }
  post {
    always {
      container('cdt') {
        archiveArtifacts allowEmptyArchive: true, artifacts: '*.log'
      }
    }
  }
}
