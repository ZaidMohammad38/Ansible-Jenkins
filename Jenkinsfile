pipeline {
    agent any

    environment {
        ANSIBLE_HOST_KEY_CHECKING = 'False'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/ZaidMohammad38/Ansible-Jenkins.git'
            }
        }

        stage('Verify Files') {
            steps {
                sh 'ls -l'
            }
        }

        stage('Validate Ansible Playbook') {
            steps {
                sh 'ansible-playbook -i inventory nginx.yml --syntax-check'
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                sh """
                sudo -u ansible ansible-playbook -i inventory nginx.yml -v
                """
            }
        }

    }

    post {

        success {
            echo 'Deployment completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the logs.'
        }

    }
}
