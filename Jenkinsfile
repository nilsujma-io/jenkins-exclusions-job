pipeline {
    agent any

    environment {
        VENV_PATH = "/var/lib/jenkins/workspace/cve-exclusions/venv"
    }

    parameters {
        string(name: 'CVE_LIST_NAME', defaultValue: 'DAMNLIST', description: 'The name of the CVE list')
        string(name: 'IMAGE_NAME', defaultValue: 'nginx:latest', description: 'The name of the image to search for CVEs')
        password(name: 'AUTH_TOKEN', defaultValue: '', description: 'Basic Authorization token for API calls')
    }

    stages {
        stage('Setup Virtual Environment') {
            steps {
                sh '''
                #!/bin/bash
                # Create the virtual environment if it does not exist
                if [ ! -d "${VENV_PATH}" ]; then
                    python3 -m venv "${VENV_PATH}"
                fi

                # Activate the virtual environment
                source "${VENV_PATH}/bin/activate"

                # Upgrade pip and install required Python modules
                pip install --upgrade pip
                pip install pandas requests

                # No need to deactivate in a non-interactive script
                '''
            }
        }

        stage('Run CVE Script') {
            steps {
                script {
                    env.CVE_LIST_NAME = params.CVE_LIST_NAME
                    env.IMAGE_NAME = params.IMAGE_NAME
                    env.AUTH_TOKEN = params.AUTH_TOKEN
                }

                sh '''
                #!/bin/bash
                # Activate the virtual environment before executing our Python script
                source "${VENV_PATH}/bin/activate"

                # Run the script. Replace 'path_to_script' with the actual script location
                python3 jenkinsjob.py

                # No need to deactivate in a non-interactive script
                '''
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
