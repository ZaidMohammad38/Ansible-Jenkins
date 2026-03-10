pipeline {
    agent any
    environment {
        ANSIBLE_HOST_KEY_CHECKING = 'False'
    }
    stages {
        stage('Pull from Git Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/rohinicc/jenkins_ansible.git'
            }
        }

        stage('Validate Ansible Playbook') {
            steps {
                sh """
                sudo -u ansible ansible-playbook -i inventory playbook.yml --check
                """
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                sh """
                sudo -u ansible ansible-playbook -i inventory playbook.yml -v
                """
            }
        }
    }

    post {
        success {
            echo 'Ansible playbook executed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs above.'
        }
    }
}
