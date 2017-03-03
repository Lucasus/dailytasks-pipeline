node('master') {
    
  stage("Checkout") {
    git credentialsId: 'DailyTasks3', url: 'https://github.com/Lucasus/DailyTasks3.git'
  }
 
  stage("Build") {
    dir('frontend') {
      sh 'npm install'
      sh 'npm run build'
    }

    dir('Web') {
      sh 'dotnet restore project.json'
      sh 'dotnet publish project.json -r ubuntu.14.04-x64'
    }
  }
    
  stage("Test") {
    dir('Tests') {
      sh 'dotnet restore project.json'
      sh 'dotnet test -xml report.xml'
      junit 'report.xml'
    }
  }
     
  stage("Archive") {
    dir('Web/bin/Debug/netcoreapp1.1/publish') {
      step([
        $class: 'WAStoragePublisher', 
        allowAnonymousAccess: false, 
        cleanUpContainer: false, 
        cntPubAccess: false, 
        containerName: 'build${BUILD_NUMBER}', 
        doNotFailIfArchivingReturnsNothing: false, 
        doNotUploadIndividualFiles: false, 
        doNotWaitForPreviousBuild: false, 
        excludeFilesPath: '', 
        filesPath: '*.*', 
        storageAccName: 'dtjenkinsstorage', 
        storageCredentialId: '281abd2d8b5a6c21214a3270131d7478', 
        uploadArtifactsOnlyIfSuccessful: false, 
        uploadZips: false, 
        virtualPath: ''
      ])
    }
  }
     
  stage name:"Deploy", concurrency: 1

  dir('Web/bin/Debug/netcoreapp1.1/publish') {
    sshagent(['9adb98ac-dfd7-4de1-8a10-968a6a4f9a16']) {
      try {
        sh  """ssh lucasus@10.30.0.5 'kill -9 `ps aux | grep \"dotnet\" | awk -F \" \" '"'"'{print \$2}'\"'\"' | head -n 1`'"""
      } 
      catch (all) {
        echo "No process to kill"
      }

      sh 'rsync -av ./ lucasus@10.30.0.5:~'
      sh 'ssh lucasus@10.30.0.5 \'dotnet Web.dll > /dev/null &\''
    }
  }
}
