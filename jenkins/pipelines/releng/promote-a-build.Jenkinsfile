pipeline {
  agent {
    kubernetes {
      label 'cdt-upload-agent-pod'
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: cdt
    image: quay.io/eclipse-cdt/cdt-infra-eclipse-full:ubuntu-16.04@sha256:9254e4c6c296f11f5a154abf181a8399e96320315fb18fec175107dbbff76c44
    tty: true
    args: ["cat"]
    resources:
      requests:
        memory: "512Mi"
        cpu: "1"
      limits:
        memory: "512Mi"
        cpu: "1"
    volumeMounts:
    - name: settings-xml
      mountPath: /home/jenkins/.m2/settings.xml
      subPath: settings.xml
      readOnly: true
    - name: m2-repo
      mountPath: /home/jenkins/.m2/repository
    - name: volume-known-hosts
      mountPath: /home/jenkins/.ssh
  volumes:
  - name: settings-xml
    configMap: 
      name: m2-dir
      items:
      - key: settings.xml
        path: settings.xml
  - name: m2-repo
    emptyDir: {}
  - name: volume-known-hosts
    configMap:
      name: known-hosts
"""
    }
  }
  parameters {
    booleanParam(defaultValue: true, description: 'Do a dry run of the build. All commands will be echoed.First run with this on, then when you are sure it is right, choose rebuild in the passing job and uncheck this box', name: 'DRY_RUN')
    choice(choices: ['cdt', 'tools.templates', 'launchbar'], description: 'Project to promote. Promoting cdt will include the cdt standalone debugger.', name: 'PROJECT')
    string(defaultValue: '9.8', description: 'The major and minor version of CDT being released (e.g. 9.7, 9.8, 10.0).', name: 'MINOR_VERSION')
    string(defaultValue: 'cdt-9.8.0', description: 'The full name of this release (e.g. cdt-9.4.2, cdt-9.5.0-rc1, cdt-9.5.0-photon-m7, cdt-9.5.0-photon-rc1)', name: 'MILESTONE')
    string(defaultValue: 'cdt-master', description: 'The CI job name being promoted from', name: 'CDT_JOB_NAME')
    string(defaultValue: '12345', description: 'The CI build number being promoted from', name: 'CDT_BUILD_NUMBER')
    choice(choices: ['releases', 'builds', 'tools.templates', 'launchbar'], description: 'Publish location (releases or builds for CDT main project, or specific sub-project location for others)', name: 'RELEASE_OR_BUILD')
  }
  stages {
    stage('Upload') {
      steps {
        container('cdt') {
            sshagent ( ['projects-storage.eclipse.org-bot-ssh']) {
              git branch: 'master', url: 'https://github.com/eclipse-cdt/cdt-infra.git'
              sh './scripts/promote-a-build.sh'
            }
        }
      }
    }
  }
}