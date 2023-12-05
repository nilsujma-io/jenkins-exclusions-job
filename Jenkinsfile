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
                # Create the virtual environment if it does not exist
                if [ ! -d "${VENV_PATH}" ]; then
                    python3 -m venv "${VENV_PATH}"
                fi

                # Activate the virtual environment
                source "${VENV_PATH}/bin/activate"

                # Upgrade pip and install required Python modules
                pip install --upgrade pip
                pip install pandas requests

                # Deactivation is not necessary in Jenkins script
                """
            }
        }

        stage('Run CVE Script') {
            steps {
                withEnv(["CVE_LIST_NAME=${params.CVE_LIST_NAME}",
                         "IMAGE_NAME=${params.IMAGE_NAME}",
                         "AUTH_TOKEN=${params.AUTH_TOKEN}"]) {
                    sh """
                    #!/bin/bash
                    # Activate the virtual environment before executing our Python script
                    source "${VENV_PATH}/bin/activate"

                    # Add the directory containing cve_script.py to the PYTHONPATH
                    export PYTHONPATH=\$PYTHONPATH:/var/lib/jenkins/workspace/cve-exclusions

                    # Run the script. Replace with the actual path if it's located in a subdirectory.
                    python3 /var/lib/jenkins/workspace/cve-exclusions/jenkinsjob.py

                    # Optional: Deactivation is not needed but can be added for completeness
                    # deactivate
                    """
                }
            }
        }
    }

    post {
        always {
            // Cleanup workspace after build
            cleanWs()
        }
    }
}

