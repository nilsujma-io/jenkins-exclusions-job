pipeline {
    agent any

    // Define the Python virtual environment path specifically for this job
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
                # Create the virtual environment if it does not exist
                if [ ! -d "${VENV_PATH}" ]; then
                    python3 -m venv "${VENV_PATH}"
                fi

                # Activate the virtual environment
                source "${VENV_PATH}/bin/activate"

                # Update pip to its latest version
                pip install --upgrade pip

                # Install the required Python modules
                pip install pandas requests

                # Deactivation is not necessary in Jenkins script, but kept for clarity
                deactivate
                '''
            }
        }

        stage('Run CVE Script') {
            steps {
                script {
                    // Pass parameters to the script as environment variables
                    env.CVE_LIST_NAME = params.CVE_LIST_NAME
                    env.IMAGE_NAME = params.IMAGE_NAME
                    env.AUTH_TOKEN = params.AUTH_TOKEN
                }

                sh '''
                # Activate the virtual environment before executing our Python script
                source "${VENV_PATH}/bin/activate"

                # Execute the script. Replace 'path_to_script' with the actual script location
                python3 jenkinsjob.py

                # Deactivation is not necessary but here for the sake of completeness
                deactivate
                '''
            }
        }
    }

    post {
        always {
            // Clean up after running
            cleanWs()
        }
    }
}
