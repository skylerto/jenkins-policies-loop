
// // node {
// // }

// pipeline {
//     agent any

//   def policies = findFiles(glob: 'policies/*.rb')

//   def parallelStagesMap = policies.collectEntries {
//       ["${it}" : generateStage(it)]
//   }

//   def generateStage(job) {
//       return {
//           stage("stage: ${job}") {
//                   echo "This is ${job}."
//                   sh script: "sleep 15"
//           }
//       }
//   }

//     stages {
//         stage('non-parallel stage') {
//             steps {
//                 echo 'This stage will be executed first.'
//             }
//         }

//         stage('parallel stage') {
//             steps {
//                 script {
//                     parallel parallelStagesMap
//                 }
//             }
//         }
//     }
// }
// main script block
// could use eg. params.parallel build parameter to choose parallel/serial 
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
      def dir, file = it.split('/')
      println("Building ${name}")
      sh(script:"cd ${dir} && chef ${name} ${file}", returnStatus:true)
    }
  }
}

// Create List of build stages to suit
// @NonCPS
// def prepareBuildStages() {
//   def buildStagesList = []

//   def policies = findFiles(glob: 'policies/*.rb')

//   def parallelStagesMap = policies.collectEntries {
//       ["${it.split('/')[1].split('.')[0]}" : generateStage(it)]
//   }

//   return parallelStagesMap
// }

// @NonCPS
// def generateStage(job) {
//     return {
//         stage("stage: ${job}") {
//           echo "This is ${job}."
//           sh script: "sleep 15"
//         }
//     }
// }
