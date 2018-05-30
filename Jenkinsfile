node {
    // checkout scm and assign its var maps to scm_output variable for future use
    stage("checkout scm") {
        checkout([$class: 'GitSCM', branches: [[name: '**']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'WipeWorkspace'], [$class: 'LocalBranch']], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '8e92e71b-b9a2-4a58-aba1-4b98b3b7666b', url: 'https://github.com/jonathanelbailey/mcit_baremetal_pipeline.git']]])
    }
    stage('Deploy Instance') {
        // get public key and output to file
        withCredentials([string(credentialsId: 'pubkey', variable: 'pubkey')]){
            wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                sh (
                    script: echo $pubkey > ${env.WORKSPACE}/roles/launch_instance/files/jbailey.pub
                )
            }
        }
        withCredentials([usernamePassword(credentialsId: 'pubkey', passwordVariable: 'sudo_pass', usernameVariable: 'sudo_user')]){
            wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                ansiblePlaybook(
                    playbook: "$env.WORKSPACE/launch_instance.yaml",
                    inventory: "$env.WORKSPACE/hosts",
                    credentialsId: 'ca75d291-93c4-47f8-aa5a-3a3b0b703d9c',
                    hostKeyChecking: false,
                    colorized: true,
                    extraVars: [
                        ansible_become_pass: [ value: "$sudo_pass", hidden: true ]
                    ]
                )
            }
        }
    }
    stage('Configure OS') {
        withCredentials([usernamePassword(credentialsId: 'pubkey', passwordVariable: 'sudo_pass', usernameVariable: 'sudo_user')]){
            wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                ansiblePlaybook(
                    playbook: "$env.WORKSPACE/launch_instance.yaml",
                    inventory: "$env.WORKSPACE/hosts",
                    credentialsId: 'ca75d291-93c4-47f8-aa5a-3a3b0b703d9c',
                    hostKeyChecking: false,
                    colorized: true,
                    extraVars: [
                        ansible_become_pass: [ value: "$sudo_pass", hidden: true ]
                    ]
                )
            }
        }
    }
    stage('Deploy Docker') {
        withCredentials([usernamePassword(credentialsId: 'pubkey', passwordVariable: 'sudo_pass', usernameVariable: 'sudo_user')]){
            wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                ansiblePlaybook(
                    playbook: "$env.WORKSPACE/configure_docker.yaml",
                    inventory: "$env.WORKSPACE/hosts",
                    credentialsId: 'ca75d291-93c4-47f8-aa5a-3a3b0b703d9c',
                    hostKeyChecking: false,
                    colorized: true,
                    extraVars: [
                        ansible_become_pass: [ value: "$sudo_pass", hidden: true ]
                    ]
                )
            }
        }
    }
    stage('Deploy Plex') {
        withCredentials([usernamePassword(credentialsId: 'pubkey', passwordVariable: 'sudo_pass', usernameVariable: 'sudo_user')]){
            wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                ansiblePlaybook(
                    playbook: "$env.WORKSPACE/configure_plex.yaml",
                    inventory: "$env.WORKSPACE/hosts",
                    credentialsId: 'ca75d291-93c4-47f8-aa5a-3a3b0b703d9c',
                    hostKeyChecking: false,
                    colorized: true,
                    extraVars: [
                        ansible_become_pass: [ value: "$sudo_pass", hidden: true ]
                    ]
                )
            }
        }
    }
}