podTemplate(label: 'test', cloud: 'kubernetes',
        containers: [
                containerTemplate(name: 'docker', image: 'docker', ttyEnabled: true, command: 'cat'),
                containerTemplate(name: 'kubectl', image: 'dtzar/helm-kubectl', ttyEnabled: true, command: 'cat')
        ], volumes: [hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')]){

    node("test") {
      source_code = checkout(scm)
      GIT_COMMIT = source_code.GIT_COMMIT.substring(0, 6).toLowerCase()
      GIT_BRANCH = source_code.GIT_BRANCH.replaceAll('origin/', '').toLowerCase()
      VERSION_IDENTIFIER = "${GIT_BRANCH}-${GIT_COMMIT}"
      APP_DOCKER_TAG = "cicd-demo:${VERSION_IDENTIFIER}"
      TESTS_DOCKER_TAG = "cicd-demo-tests:${VERSION_IDENTIFIER}"
      container('docker'){
        parallel(
          'buildApp': {stage('build app'){
            sh "docker build -f ./hello-world/src/Dockerfile -t ${APP_DOCKER_TAG}  ./hello-world/src"
          }},
          'buildTests': {stage('build tests'){
            sh "docker build -f ./hello-world/integration/Dockerfile -t ${TESTS_DOCKER_TAG} ./hello-world/integration"
          }}
        )
      }
    }
}