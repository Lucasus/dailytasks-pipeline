def ouputPackageDir = 'frontend/dist'

node('master') {
    
  stage("Checkout") {
    git credentialsId: 'DailyTasks3', url: 'https://github.com/Lucasus/DailyTasks3.git', branch: 'fastnote'
  }
 
  stage("Build") {

    sh 'git clean -xdf'
      
    dir('frontend') {
      sh 'npm install'
      sh 'npm run build'
    }
  }    
 
  stage("Archive") {
    dir(ouputPackageDir) {
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
        filesPath: '**/*.*', 
        storageAccName: '33ptxumln7fdkfastnote', 
        storageCredentialId: 'fastnoteartifacts', 
        uploadArtifactsOnlyIfSuccessful: false, 
        uploadZips: false, 
        virtualPath: ''
      ])
    }
  }
     
  stage name:"Deploy", concurrency: 1

  dir(ouputPackageDir) {
    sshagent(['9adb98ac-dfd7-4de1-8a10-968a6a4f9a16']) {
      sh 'ssh lucasus@10.30.0.5 \'rm * -rf\''
      sh 'rsync -av ./ lucasus@10.30.0.10:~'
    }
  }
}
