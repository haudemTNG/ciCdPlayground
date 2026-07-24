pipeline {
    agent any
    parameters {
      string(name: 'Version', defaultValue: '1.0.0', description: 'Please provide version number.')
    tools {
        nodejs 'yarn'
    }

    stages {
       stage('pre') {
          steps {
        script {
          currentBuild.displayName = 'displayName'
          currentBuild.description = "${params.Version}"
                }
            }
      }
        stage('install') {
            steps {
                sh 'yarn'
            }
        }

        stage('build') {
            steps {
                sh 'yarn build'
            }
        }

        stage('test:unit') {
            steps {
                sh 'yarn test'

          }
          post {
              always {
                  junit 'reports/jest-junit.xml'
              }
          }
      }

        stage('test:e2e') {
            steps {
                sh 'yarn test:e2e'
          }
          post {
              always {
                  junit 'reports/cypress-junit.xml'
              }
          }
      }

        stage('deploy') {
            steps {
                s3Upload consoleLogLevel: 'INFO', 
                  dontSetBuildResultOnFailure: false, 
                  dontWaitForConcurrentBuildCompletion: false, 
                  entries: [[
                      bucket: "cicd-workshop-playground/${env.GIT_URL.split('/')[3]}", 
                      excludedFile: '', 
                      flatten: false, 
                      gzipFiles: false, 
                      keepForever: false, 
                      managedArtifacts: false, 
                      noUploadOnFailure: false, 
                      selectedRegion: 'eu-central-1', 
                      showDirectlyInBrowser: false, 
                      sourceFile: 'public/**/*.*', 
                      storageClass: 'STANDARD', 
                      uploadFromSlave: false, 
                      useServerSideEncryption: false
                    ]], 
                    pluginFailureResultConstraint: 'FAILURE', 
                    profileName: 'role-based-access', 
                    userMetadata: []
            }
        }
    }
}