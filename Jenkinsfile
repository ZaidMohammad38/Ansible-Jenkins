pipeline {
    agent any
    environment {
        ANSIBLE_HOST_KEY_CHECKING = 'False'
    }
    stages {
        stage('Pull from Git Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/ZaidMohammad38/Ansible-Jenkins.git'
            }
        }

        stage('Validate Ansible Playbook') {
            steps {
                sh """
                ansible-playbook -i inventory nginx.yml -vvv
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
