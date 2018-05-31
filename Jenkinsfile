node {
    // checkout scm and assign its var maps to scm_output variable for future use
    stage("checkout scm") {
        checkout([$class: 'GitSCM', branches: [[name: '**']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'WipeWorkspace'], [$class: 'LocalBranch']], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '8e92e71b-b9a2-4a58-aba1-4b98b3b7666b', url: 'https://github.com/jonathanelbailey/mcit_plex.git']]])
    }
    stage('Configure Openstack') {
        withCredentials([usernamePassword(credentialsId: 'ca75d291-93c4-47f8-aa5a-3a3b0b703d9c', passwordVariable: 'sudo_pass', usernameVariable: 'sudo_user')]){
            wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                ansiblePlaybook(
                    playbook: "$env.WORKSPACE/configure_openstack.yaml",
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
    stage('Deploy Openstack Instance') {
        withCredentials(
            [
                usernamePassword(credentialsId: 'ca75d291-93c4-47f8-aa5a-3a3b0b703d9c', passwordVariable: 'sudo_pass', usernameVariable: 'sudo_user'), 
                file(credentialsId: 'pubkey', variable: 'pubkey')
            ]){
            wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                ansiblePlaybook(
                    playbook: "$env.WORKSPACE/launch_instance.yaml",
                    inventory: "$env.WORKSPACE/hosts",
                    credentialsId: 'ca75d291-93c4-47f8-aa5a-3a3b0b703d9c',
                    hostKeyChecking: false,
                    colorized: true,
                    extraVars: [
                        ansible_become_pass: [ value: "$sudo_pass", hidden: true ],
                        pubkey: [ value: "$pubkey", hidden: true ],
                        hosts: "$env.WORKSPACE/hosts"
                    ]
                )
            }
        }
    }
    stage('Configure OS') {
        withCredentials([sshUserPrivateKey(credentialsId: 'jbailey_centos', keyFileVariable: 'key_file')]){
            wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                ansiblePlaybook(
                    playbook: "$env.WORKSPACE/configure_os.yaml",
                    inventory: "$env.WORKSPACE/hosts",
                    credentialsId: 'jbailey_centos',
                    hostKeyChecking: false,
                    colorized: true
                )
            }
        }
    }
    stage('Deploy Docker') {
        withCredentials([sshUserPrivateKey(credentialsId: 'jbailey_centos', keyFileVariable: 'key_file')]){
            wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                ansiblePlaybook(
                    playbook: "$env.WORKSPACE/configure_docker.yaml",
                    inventory: "$env.WORKSPACE/hosts",
                    credentialsId: 'jbailey_centos',
                    hostKeyChecking: false,
                    colorized: true
                )
            }
        }
    }
    stage('Deploy Plex') {
        withCredentials([sshUserPrivateKey(credentialsId: 'jbailey_centos', keyFileVariable: 'key_file')]){
            wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                ansiblePlaybook(
                    playbook: "$env.WORKSPACE/configure_plex.yaml",
                    inventory: "$env.WORKSPACE/hosts",
                    credentialsId: 'jbailey_centos',
                    hostKeyChecking: false,
                    colorized: true
                )
            }
        }
    }
    stage('Deploy SabNZBD') {
        withCredentials([sshUserPrivateKey(credentialsId: 'jbailey_centos', keyFileVariable: 'key_file')]){
            wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                ansiblePlaybook(
                    playbook: "$env.WORKSPACE/configure_sabnzbd.yaml",
                    inventory: "$env.WORKSPACE/hosts",
                    credentialsId: 'jbailey_centos',
                    hostKeyChecking: false,
                    colorized: true
                )
            }
        }
    }
    stage('Deploy Sonarr') {
        withCredentials([sshUserPrivateKey(credentialsId: 'jbailey_centos', keyFileVariable: 'key_file')]){
            wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                ansiblePlaybook(
                    playbook: "$env.WORKSPACE/configure_sonarr.yaml",
                    inventory: "$env.WORKSPACE/hosts",
                    credentialsId: 'jbailey_centos',
                    hostKeyChecking: false,
                    colorized: true
                )
            }
        }
    }
    stage('Deploy Radarr') {
        withCredentials([sshUserPrivateKey(credentialsId: 'jbailey_centos', keyFileVariable: 'key_file')]){
            wrap([$class: 'AnsiColorBuildWrapper', colorMapName: "xterm"]) {
                ansiblePlaybook(
                    playbook: "$env.WORKSPACE/configure_radarr.yaml",
                    inventory: "$env.WORKSPACE/hosts",
                    credentialsId: 'jbailey_centos',
                    hostKeyChecking: false,
                    colorized: true
                )
            }
        }
    }
}