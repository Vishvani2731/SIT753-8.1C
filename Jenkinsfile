pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building the project...'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }

       stage('Security Scan') {
    steps {
        bat '''
        echo Running lightweight OWASP Dependency-Check...
        mkdir dependency-check
        curl -L -o dependency-check.zip https://github.com/jeremylong/DependencyCheck/releases/download/v8.4.0/dependency-check-8.4.0-release.zip
        powershell -Command "Expand-Archive dependency-check.zip -DestinationPath dependency-check -Force"
        
        REM Only scan the "lib" or "src" folder instead of the whole repo
        dependency-check\\dependency-check\\bin\\dependency-check.bat --project "MyProject" --scan src --format HTML
        '''
    }
}

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
            }
        }
    }
}
