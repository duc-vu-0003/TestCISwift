pipeline {
    agent any
    options {
        // Keep the 10 most recent builds
        buildDiscarder(logRotator(numToKeepStr:'10'))
    }
    stages {
        stage ('Start') {
              steps {
                // send build started notifications
                sendNotifications 'STARTED'
								sh 'cd fastlane'
              }
        }

        stage("Stage Checkout") {
            steps {
                checkout scm
                sh 'git submodule update --init'
            }
        }

        stage('Stage Build') {
            steps {
                //branch name from Jenkins environment variables
                echo "My branch is: ${env.BRANCH_NAME}"
                script {
                    def changeLog = getChangelog()
                    echo "Change logs: ${changeLog}"
                }
								archive 'reports/, dist/'
            }
        }

        //stage('Stage Upload To Fabric') {
        //    steps {
        //        sh "./gradlew crashlyticsUploadDistributionDebug -PBUILD_NUMBER=${env.BUILD_NUMBER}"
        //    }
        //}

        stage('SonarQube analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarQubeScanner';
                    withSonarQubeEnv('Sonar') {
                        // requires SonarQube Scanner for Gradle 2.1+
                        // It's important to add --info because of SONARJNKNS-281
                        // sh "${scannerHome}/bin/sonar-scanner"
												ansiColor('xterm') {
    											sh 'fastlane metrics'
												}
                    }
                }
            }
        }

    }
    post {
        always {
          sendNotifications currentBuild.result
        }
    }
}

def getChangelog() {
    def changeLogSets = currentBuild.rawBuild.changeSets
    def changelog = ""
    for (int i = 0; i < changeLogSets.size(); i++) {
        def entries = changeLogSets[i].items
        for (int j = 0; j < entries.length; j++) {
            def entry = entries[j]
            changelog += entry.msg + '\n'
        }
    }
    return changelog
}

def getQualityGate() {
    timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
        def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
        if (qg.status != 'OK') {
            error "Pipeline aborted due to quality gate failure: ${qg.status}"
        }
    }
}

def sendNotifications(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"
  def details = """<p>${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
    <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>"""

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)

  emailext (
      to: 'ducva2410@gmail.com',
      subject: subject,
      body: details,
      recipientProviders: [[$class: 'DevelopersRecipientProvider']]
    )
}
