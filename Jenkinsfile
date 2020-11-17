

node{

properties([
  parameters([
    credentials(credentialType: 'com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl',
                defaultValue: '',
                description: 'Test creds uname with pass',
                // the credentials name used here must match the credentialsId referenced in the 'Deploy' stage below
                name: 'GITHUB_SSH_CREDENTIALS_ID',
                required: true),                
    credentials(credentialType: 'com.cloudbees.plugins.credentials.impl.BasicSSHUserPrivateKey',
                 defaultValue: '',
                 description: 'Test creds uname with key',
                 // the credentials name used here must match the credentialsId referenced in the 'Deploy' stage below
                 name: 'GITHUB_SSH_KEY_CREDENTIALS_ID',
                 required: true),
    string(defaultValue: 'master',
            name: 'MASTER_BRANCH',
            description: 'branch to build from'),
    string(defaultValue: 'alkilpio',
            name: 'GIT_REPO_OWNER',
            description: 'repo to build from'),        
  ] 

  )
])
     stage('Get') {
        // parameters([
        // credentials(credentialType: 'com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl',
        //         defaultValue: '',
        //         description: 'Test creds uname with pass',
        //         // the credentials name used here must match the credentialsId referenced in the 'Deploy' stage below
        //         name: 'GITHUB_SSH_CREDENTIALS_ID',
        //         required: true)])

         echo 'Stage <Get>'

withCredentials([usernameColonPassword(credentialsId: 'GITHUB_SSH_CREDENTIALS_ID', variable: 'USERPASS')]) {
    ```echo ${USERPASS} > /tmp/USERPASS
       cat /tmp/USERPASS
    ```   
  }



//         def returnValue = input message: 'Need some input', parameters: [string(defaultValue: '', description: '', name: 'Give me a value')]
      //   checkoutFromGithubToSubfolder('jmtest', MASTER_BRANCH)
        def repositoryName ='jmtest'
        def clean = true
        def branch = 'master'
        def extensions = [[$class: 'RelativeTargetDirectory', relativeTargetDir: "${repositoryName}"],
                    [$class: 'UserIdentity', name: "${GIT_REPO_OWNER}"],
                    [$class: 'ScmName', name: "${GIT_REPO_OWNER}"]
    ]
   
    if (clean) {
        extensions.push([$class: 'WipeWorkspace']) //CleanBeforeCheckout
    }


echo "++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++"
    checkout([$class                           : 'GitSCM', branches: [ [name: "*/${MASTER_BRANCH}"], [name: "*/${branch}"]],
            doGenerateSubmoduleConfigurations: false, submoduleCfg: [],
            userRemoteConfigs                : [[credentialsId: GITHUB_SSH_CREDENTIALS_ID, url: "git@github.com:${GIT_REPO_OWNER}/${repositoryName}.git"]],
           extensions                       : extensions
    ])





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

    checkout([$class                           : 'GitSCM', branches: [ [name: "*/${MASTER_BRANCH}"], [name: "*/${branch}"]],
            doGenerateSubmoduleConfigurations: false, submoduleCfg: [],
            userRemoteConfigs                : [[credentialsId: GITHUB_SSH_CREDENTIALS_ID, url: "git@github.com:${GIT_REPO_OWNER}/${repositoryName}.git"]],
           extensions                       : extensions
    ])
    }


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
