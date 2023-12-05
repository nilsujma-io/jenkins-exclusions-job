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
                sh """
                #!/bin/bash
                if [ ! -d "${VENV_PATH}" ]; then
                    /usr/bin/env python3 -m venv "${VENV_PATH}"
                fi
                . "${VENV_PATH}/bin/activate"
                pip install --upgrade pip
                pip install pandas requests openpyxl
                """
            }
        }

        stage('Run CVE Script') {
            steps {
                // Bind the password parameter to an environment variable
                withCredentials([string(credentialsId: 'AUTH_TOKEN', variable: 'AUTH_TOKEN')]) {
                    sh """
                    #!/bin/bash
                    . "${VENV_PATH}/bin/activate"
                    export CVE_LIST_NAME="${params.CVE_LIST_NAME}"
                    export IMAGE_NAME="${params.IMAGE_NAME}"
                    export AUTH_TOKEN="${AUTH_TOKEN}"

                    # Diagnostic command to ensure variables are set (Remove after testing. Do NOT print AUTH_TOKEN)
                    echo "CVE_LIST_NAME: $CVE_LIST_NAME"
                    echo "IMAGE_NAME: $IMAGE_NAME"

                    python3 jenkinsjob.py
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

