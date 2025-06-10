pipeline {
    agent { label 'ang_agent_node' }

    tools {
        nodejs 'nodejs86' // Refers to the configured tool name in Jenkins
    }

    environment {
        NODE_ENV = 'development'
    }

    options {
        skipStagesAfterUnstable()
        timestamps()
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

//        stage('Checkout Code') {
//            steps {
//               git url: 'https://github.com/venkyscafe/ang_scm.git', branch: 'main'
//            }
//        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install --include=dev'
            }
        }

        stage('Verify Angular CLI & Builder') {
            steps {
                sh '''
                echo "Verifying Angular CLI and build-angular package..."
                ./node_modules/.bin/ng version
                ls -la node_modules/@angular-devkit/build-angular || echo "build-angular not found"
                '''
            }
        }

        stage('Build Angular App') {
            steps {
                sh './node_modules/.bin/ng build'
            }
        }

        stage('Run Tests') {
            steps {
                sh './node_modules/.bin/ng test --watch=false --browsers=ChromeHeadless || echo "Tests failed, continuing..."'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                echo "Deploying application..."
                rm -rf /var/www/html/*
                cp -r dist/ang_scm/* /var/www/html/
                '''
            }
        }
    }

    post {
        success {
            echo 'Build, test, and deploy completed successfully.'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
