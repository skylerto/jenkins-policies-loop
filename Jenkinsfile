
// node {
// }

pipeline {
    agent any

  def policies = findFiles(glob: 'policies/*.rb')

  def parallelStagesMap = policies.collectEntries {
      ["${it}" : generateStage(it)]
  }

  def generateStage(job) {
      return {
          stage("stage: ${job}") {
                  echo "This is ${job}."
                  sh script: "sleep 15"
          }
      }
  }

    stages {
        stage('non-parallel stage') {
            steps {
                echo 'This stage will be executed first.'
            }
        }

        stage('parallel stage') {
            steps {
                script {
                    parallel parallelStagesMap
                }
            }
        }
    }
}
