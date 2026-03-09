pipeline {
    agent any

    stages {

        stage('Clone Repo') {
            steps {
                git 'https://github.com/ZaidMohammad38/Ansible-Jenkins.git'
            }
        }

        stage('Run Playbook') {
            steps {
                sh 'ansible-playbook -i inventory nginx.yml'
            }
        }

    }
}
