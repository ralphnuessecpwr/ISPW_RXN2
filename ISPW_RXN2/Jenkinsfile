def automaticBuildFileName  = 'automaticBuildParams.txt'

def hciConnectionId         = '166b3097-0c13-4d9d-939c-b098e6098405'
def hostCredentialsId       = 'hostCredentials'
def gitCredentialsId        = 'GitCredentials'
def cesCredentialsId        = 'jenkinsCesToken'
def gitRepoUrl              = 'https://github.com/ralphnuessecpwr/ISPW_RXN1.git'
def ispwStream              = 'FTSDEMO'
def ispwApplication         = 'RXN1'

//**********************************************************************
// Start of Script
//**********************************************************************
node {
  stage ('Checkout and initialize') {
    // Clear workspace
    dir('./') {
      deleteDir()
    }

    checkout scm

  }

  stage('Load code to mainframe') {

    gitToIspwIntegration app:   ispwApplication, 
        branchMapping:          '''*master* => QA1, per-branch
            *RXN1* => QA1, per-branch''',
        connectionId:           hciConnectionId, 
        credentialsId:          hostCredentialsId, 
        gitCredentialsId:       gitCredentialsId, 
        gitRepoUrl:             gitRepoUrl, 
        runtimeConfig:          'ispw', 
        stream:                 ispwStream
    
  }

  // If the automaticBuildParams.txt has not been created, it means no programs
  // have been changed and the pipeline was triggered for other changes (in configuration files)
  // These changes do not need to be "built".
  try {
    automaticBuildInfo = readJSON(file: automaticBuildFileName)
  }
  catch(Exception e) {

    echo "No Automatic Build Params file was found.\n" +
      "Meaning, no programs have been changed.\n" +
      "Job gets ended prematurely, but successfully."
    currentBuild.result = 'SUCCESS'
    return

  }
  
  stage('Build mainframe code') {
  /*
    // Generate all tasks first, in case no dependencies are found
    ispwOperation connectionId:   hciConnectionId,
      consoleLogResponseBody:     true, 
      credentialsId:              cesCredentialsId,       
      ispwAction:                 'GenerateTasksInAssignment', 
      ispwRequestBody:            """
        assignmentId=${automaticBuildInfo.containerId}
        level=${automaticBuildInfo.taskLevel}
        """
  */
    
    ispwOperation connectionId:   hciConnectionId, 
        consoleLogResponseBody:   true, 
        credentialsId:            cesCredentialsId,       
        ispwAction:               'BuildTask', 
        ispwRequestBody:          '''buildautomatically = true'''

  }
}