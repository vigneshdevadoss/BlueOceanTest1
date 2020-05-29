import groovy.xml.XmlUtil
def failedRepos = []
def repoPackagesMap = [:]
def repoAclMap = [:]
def needVariableSub = false
def repoVarSubMap = [:]
pipeline {
  agent any
  parameters {
    string(name: 'reposToProcess', defaultValue: '', description: 'Comma Seperated Values of the repos to process in this job')
    booleanParam(name: 'overrideVarSubAsFalse', defaultValue: true, description: '')
  }
  options {
    buildDiscarder(logRotator(daysToKeepStr: '2',numToKeepStr: '10'))
  }
  environment {
    pipelineBranch = 'dev'
    targetEnvironment = "dev"
  }
  stages {
    stage('Initialize') {
      steps {
        script {
          echo "Build NUmber used for the build is ${BUILD_ID}"
        }
      }
   } // end of initialize stage
    stage('Parallel-Checkout') {
      steps {
          script {
              def reposToProcessArr = params.reposToProcess.split("\\s*,\\s*")
              def parallelJobs = [:]
              reposToProcessArr.each { repo ->
                  parallelJobs["${repo}"] = {
                    stage("${repo}") {
                      try {
                        echo "Processing Start:"
                        echo "Processing End"
                      }
                      catch (err) {
                        echo "Inside Catch Repo Failed is ${repo}"
                        failedRepos.add("${repo}")
                        println(err.toString());
                        println(err.getStackTrace());
                        error "Exception in Checkout for repo"
                      }
                    }
                  }
              }
              parallelJobs.failFast = true
              parallel parallelJobs
          }
      }
      post {
        always {
          echo "Failed Repos is ${failedRepos}"
        }
      }
    } // end of Parallel-Checkout Stage
    stage('ABE-Build') {
      steps {
        echo "Hallo in ABE-Build"
      }
    } // end of ABE-Build
    stage('Deploy') {
      steps {
        echo "Deploy"
      }
    } // end of deploy stage
  } // end of stages
} // end of pipeline
