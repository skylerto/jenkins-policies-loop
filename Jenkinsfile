def runParallel = true
def buildStages

node('master') {
  stage('Initialise') {
    checkout scm
    // Set up List<Map<String,Closure>> describing the builds
    buildStages = prepareBuildStages()
    println("Initialised pipeline.")
  }

  for (builds in buildStages) {
    if (runParallel) {
      parallel(builds)
    } else {
      // run serially (nb. Map is unordered! )
      for (build in builds.values()) {
        build.call()
      }
    }
  }

  stage('Finish') {
      println('Build complete.')
  }
}

// Create List of build stages to suit
def prepareBuildStages() {
  def buildStagesList = []
  def policies = findFiles(glob: 'policies/*.rb')

  // for (i=1; i<=policies.length(); i++) {

  for (policy in policies ) {
    def buildParallelMap = [:]
    for (name in ['install', 'push']) {
      def n = "${policy} ${name}"
      buildParallelMap.put(n, prepareOneBuildStage(policy, name))
    }
    buildStagesList.add(buildParallelMap)
  }
  return buildStagesList
}

def prepareOneBuildStage(policy, name) {
  return {
    stage("${name} ${policy}") {
      def dir = policy.toString().split('/')[0]
      def file = policy.toString().split('/')[1]
      println("Building ${name}")
      sh(script:"cd ${dir} && chef ${name} ${file}", returnStatus:true)
    }
  }
}
