pipeline {
    agent any
    stages {
        stage('Setup: Install Puppet & Ansible (Localhost)') {
            steps {
                echo '🔧 Installing Puppet and Ansible on localhost...'
                sh '''
                    sudo apt update
                    echo "Installing Puppet..."
                    sudo apt install -y wget gnupg lsb-release curl
                    wget https://apt.puppet.com/puppet7-release-$(lsb_release -cs).deb
                    sudo dpkg -i puppet7-release-*.deb
                    sudo apt update
                    sudo apt install -y puppet-agent
                    sudo /opt/puppetlabs/bin/puppet resource service puppet ensure=running enable=true
                    echo "Installing Ansible..."
                    sudo apt install -y software-properties-common
                    sudo add-apt-repository --yes --update ppa:ansible/ansible
                    sudo apt update
                    sudo apt install -y ansible
                '''
            }
        }

        stage('Job 2: Install Docker using Ansible') {
            steps {
                echo 'Running Ansible playbook to install Docker on localhost...'
                writeFile file: 'install_docker.yml', text: '''
                - hosts: localhost
                  connection: local
                  become: true
                  tasks:
                    - name: Install Docker
                      apt:
                        name: docker.io
                        state: present
                        update_cache: yes
                '''
                sh '''
                    ansible-playbook install_docker.yml
                '''
            }
        }

        stage('Job 3: Clone, Build and Run PHP App in Docker') {
            steps {
                echo 'Deploying PHP Docker container...'
                sh '''
                    rm -rf projCert
                    git clone https://github.com/tverma123/final-project-2.git
                    cd projCert
                    docker build -t php-webapp .
                    docker run -d -p 8080:80 --name php-site php-webapp || echo "Container may already exist"
                '''
            }
        }
    }
}
