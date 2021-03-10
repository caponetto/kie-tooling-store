#!/usr/bin/env groovy

node('rhel8'){
    stage('Test - Install build requirements')
    def nodeHome = tool 'nodejs-12.13.1'
    env.PATH="${env.PATH}:${nodeHome}/bin"
    sh 'node -v'
    sh 'npm -v'

    stage('Test - Download VSIX')
    sh 'wget "https://github.com/kiegroup/kie-tooling-store/releases/download/$VERSION/vscode_extension_bpmn_editor_$VERSION.vsix"'
    sh 'wget "https://github.com/kiegroup/kie-tooling-store/releases/download/$VERSION/vscode_extension_dmn_editor_$VERSION.vsix"'
    sh 'wget "https://github.com/kiegroup/kie-tooling-store/releases/download/$VERSION/vscode_extension_red_hat_business_automation_bundle_$VERSION.vsix"'
    sh 'md5sum *.vsix'

    stage('Test - Upload VSIX files to staging')
    def vsix = findFiles(glob: '**.vsix')
    stash name:'vsix'
    archiveArtifacts artifacts: '**.vsix'
}

node('rhel8'){
    if(publishToMarketPlace.equals('true')){
        timeout(time:5, unit:'DAYS') {
            input message:'Approve deployment?', submitter: 'gcaponet,tfernand'
        }

        stage('Test - Publish to VS Code Marketplace')
        unstash 'vsix'
        def vsix = findFiles(glob: '**.vsix')
        sh 'npm install -g vsce'
        // publish
    }// if publishToMarketPlace
}

node('rhel8'){
    if(publishToOVSX.equals('true')){
        timeout(time:5, unit:'DAYS') {
            input message:'Approve deployment?', submitter: 'gcaponet'
        }

        stage('Test - Publish to Open-vsx Marketplace')
        unstash 'vsix'
        def vsix = findFiles(glob: '**.vsix')
        sh "npm install -g ovsx"
        // publish
    }// if publishToOVSX
}
