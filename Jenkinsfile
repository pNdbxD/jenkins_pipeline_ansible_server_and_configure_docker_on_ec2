pipeline {
    agent any

    environment {
        ANSIBLE_SERVER = "170.64.139.115"
    }

    stages {
        stage('Copy files to ansible server') {
            steps {
                echo 'Copying files to ansible control server...'

                sshagent(['jenkins_DO1_droplet_ssh_key']) {

                    sh '''
                        scp -o StrictHostKeyChecking=no \
                            ansible/* \
                            root@${ANSIBLE_SERVER}:/root/ansible
                    '''

                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                            root@${ANSIBLE_SERVER} \
                            "mkdir -p /root/.ssh && chmod 700 /root/.ssh"
                    '''

                    withCredentials([
                        sshUserPrivateKey(
                            credentialsId: 'id_ed25519_ansible_aws1',
                            keyFileVariable: 'SSH_KEY_FILE',
                            usernameVariable: 'SSH_USER'
                        )
                    ]) {

                        sh '''
                            scp -o StrictHostKeyChecking=no \
                                "$SSH_KEY_FILE" \
                                root@${ANSIBLE_SERVER}:/root/.ssh/id_ed25519_aws_jenkins_tf.pem

                            ssh -o StrictHostKeyChecking=no \
                                root@${ANSIBLE_SERVER} \
                                "chmod 600 /root/.ssh/id_ed25519_aws_jenkins_tf.pem"
                        '''
                    }
                }
            }
        }

        stage('Run Ansible playbook') {
            steps {
                echo 'Running Ansible playbook on ansible control server to configure EC2 instances...'

                script {
                    def remote = [:]

                    remote.name = 'ansible-server'
                    remote.host = ANSIBLE_SERVER
                    remote.allowAnyHosts = true

                    withCredentials([
                        sshUserPrivateKey(
                            credentialsId: 'jenkins_DO1_droplet_ssh_key',
                            keyFileVariable: 'SSH_KEY_FILE',
                            usernameVariable: 'SSH_USER'
                        )
                    ]) {

                        remote.user = SSH_USER
                        remote.identityFile = SSH_KEY_FILE

                        sshCommand remote: remote, command: 'whoami && hostname && pwd && ls -la'
                        sshCommand remote: remote, command: 'cd ansible && ls'
                        sshScript remote: remote, script: 'ansible/prepare_ansible_server.sh'

                        sshCommand remote: remote, command: 'cd ansible && ansible-playbook install_docker.yaml'
                    }
                }
            }
        }
    }
}