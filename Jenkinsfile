pipeline {
    agent any

    parameters {
        string(name: 'CVE_LIST_NAME', defaultValue: 'DAMNLIST', description: 'The name of the CVE list')
        string(name: 'IMAGE_TO_SEARCH', defaultValue: 'nginx:latest', description: 'The name of the image to search for CVEs')
        password(name: 'AUTH_TOKEN', defaultValue: '', description: 'Basic Authorization token for API calls')
    }

    environment {
        // Define environment variable AUTH_TOKEN that will hold the authorization token
        AUTH_TOKEN = "${params.AUTH_TOKEN}"
    }

    stages {
        stage('Run CVE Script') {
            steps {
                sh '''
                # Activate your Python environment if needed. Uncomment the next line if required.
                # source path_to_your_virtualenv/bin/activate
                python cve_script.py
                '''
            }
        }
    }
}
