
 node{

     stage('Get') {
         echo 'Stage <Get>'
//         def returnValue = input message: 'Need some input', parameters: [string(defaultValue: '', description: '', name: 'Give me a value')]
         checkoutFromGithubToSubfolder('jmtest', MASTER_BRANCH)
     }
    
     stage ('Modify'){
         modifyReadmeMd('master')
     }
    
     stage('UpdateAndCommit'){
         echo 'Stage <UpdateAndCommit>'
        
         commitBranch('master')
     }
    stage('GitPush'){
        echo 'Stage <GitPush>'
     dir('jmtest'){
      
      sshagent(credentials: ['${GITHUB_SSH_KEY_CREDENTIALS_ID}']) {
        def branchName='master'
        sh "git config user.name ${GIT_REPO_OWNER}"
        sh "git checkout ${branchName}"
        sh("git push -u origin ${branchName}")
        //gitPushToBranch('master')
        }
     }
    }
    
}


def checkoutFromGithubToSubfolder(repositoryName, def branch = 'master', def clean = true) {
    def extensions = [[$class: 'RelativeTargetDirectory', relativeTargetDir: "${repositoryName}"],
                    [$class: 'UserIdentity', name: "${GIT_REPO_OWNER}"],
                    [$class: 'ScmName', name: "${GIT_REPO_OWNER}"]
    ]
   
    if (clean) {
        extensions.push([$class: 'WipeWorkspace']) //CleanBeforeCheckout
    }
// withCredentials([usernamePassword(credentialsId: '${GITHUB_SSH_CREDENTIALS_ID}', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
    checkout([$class                           : 'GitSCM', branches: [ [name: "*/${MASTER_BRANCH}"], [name: "*/${branch}"]],
            doGenerateSubmoduleConfigurations: false, submoduleCfg: [],
            userRemoteConfigs                : [[credentialsId: '${GITHUB_SSH_CREDENTIALS_ID}', url: "git@github.com:${GIT_REPO_OWNER}/${repositoryName}.git"]],
//              userRemoteConfigs                : [[url: "git@github.com:${GIT_REPO_OWNER}/${repositoryName}.git"]],
           extensions                       : extensions
    ])
 }
//}


void commitBranch(branchName) {
                dir('jmtest') {
                sh "git checkout ${branchName}"
                sh "git commit -m '${branchName}' || true"
                }
}

private void gitPushToBranch(branchName) {
        sshagent(credentials: [${GITHUB_SSH_CREDENTIALS_ID}]) {
        sh "git config user.name ${GIT_REPO_OWNER}"
        sh "git checkout ${branchName}"
        sh("git push -u origin ${branchName}")
    }
}


void modifyReadmeMd(branchName) {
    dir('jmtest') {
    sh "git checkout ${branchName}"
    def fileContent = readFile 'README.md'
    writeFile file: 'README.md', text: "${fileContent}\n${GIT_REPO_OWNER}"
    sh "git add README.md" 
    }
}
