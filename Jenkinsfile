def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger'
]

pipeline {
    agent  { label 'node' } 

    stages {
        stage('Install Ansible') {
            steps {
                script {
                    def ansibleInstalled = sh(script: 'which ansible', returnStatus: true)
                    if (ansibleInstalled != 0) {
                        echo "Ansible is not installed. Installing now..."
                        sh '''
                            sudo apt-add-repository -y ppa:ansible/ansible
                            sudo apt update
                            sudo apt install -y ansible
                        '''
                    } else {
                        echo "Ansible is already installed. Skipping installation."
                    }
                }
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                script {
                    sshagent(['ansible-ssh-agent']) {
                        sh '''#!/bin/bash
                        ansible all -i inventory.ini -m ping
                        ansible-playbook -i inventory.ini playbook.yml 
                        '''
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'Jenkins Build Alert'
            slackSend channel: 'jenkins',
                 color: COLOR_MAP[currentBuild.currentResult],
                 message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n Access logs at: ${env.BUILD_URL}"
        }
    }
}    
