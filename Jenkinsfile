pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install Apache2') {
      steps {
        script {
          echo 'Running Ansible playbook to install Apache2...'
        }
        sh 'ansible-playbook -i inventory.ini install_apache2.yml'
      }
    }
  }

  post {
    success {
      echo 'Apache installation completed successfully.'
    }
    failure {
      echo 'Apache installation failed. Please check the Jenkins console output and Ansible logs.'
    }
    always {
      echo 'Pipeline finished. Performing cleanup.'
      cleanWs()
    }
  }
}
