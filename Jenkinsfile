#!/usr/bin/env groovy

node('rhel8'){
    stage('Install requirements'){
        def nodeHome = tool 'nodejs-12.13.1'
        env.PATH="${env.PATH}:${nodeHome}/bin"
        sh 'node -v'
        sh 'npm -v'
    }

    stage('Download VSIX files') {
        sh 'wget "https://github.com/kiegroup/kie-tooling-store/releases/download/$VERSION/vscode_extension_bpmn_editor_$VERSION.vsix"'
        sh 'wget "https://github.com/kiegroup/kie-tooling-store/releases/download/$VERSION/vscode_extension_dmn_editor_$VERSION.vsix"'
        sh 'wget "https://github.com/kiegroup/kie-tooling-store/releases/download/$VERSION/vscode_extension_red_hat_business_automation_bundle_$VERSION.vsix"'
        sh 'md5sum *.vsix'
    }

    stage('Archive VSIX files') {
        def vsix = findFiles(glob: '**.vsix')
        stash name:'vsix', includes: '**.vsix'
        archiveArtifacts artifacts: '**.vsix'
    }
}

node('rhel8'){
    if(publishToMarketPlace.equals('true')){
        timeout(time:5, unit:'DAYS') {
            input message:'Approve deployment?', submitter: 'gcaponet,tfernand'
        }

        stage('Publish to VS Code Marketplace') {
            unstash 'vsix'
            def vsix = findFiles(glob: '**.vsix')
            sh 'npm install -g vsce'
            // publish here
        }
    }
}

node('rhel8'){
    if(publishToOVSX.equals('true')){
        timeout(time:5, unit:'DAYS') {
            input message:'Approve deployment?', submitter: 'gcaponet,tfernand'
        }

        stage('Publish to Open-vsx Marketplace') {
            unstash 'vsix'
            def vsix = findFiles(glob: '**.vsix')
            sh "npm install -g ovsx"
            // publish here
        }
    }
}
