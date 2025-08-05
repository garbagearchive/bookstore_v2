pipeline {
    agent any

    environment {
        DEPLOY_PATH = 'C:\\deploy\\bookstore'
        JAR_NAME = 'basic_bookstore_spring-0.0.1-SNAPSHOT.jar' // sửa theo đúng tên file jar thực tế sau khi build
    }

    stages {
        stage('Clone') {
            steps {
                echo 'Cloning project from GitHub...'
                git branch: 'main', url: 'https://github.com/garbagearchive/basic_bookstore_spring.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building with Maven...'
                dir('bookstore') {
                    bat 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                dir('bookstore') {
                    bat 'mvn test'
                }
            }
        }

        stage('Copy JAR') {
            steps {
                echo 'Copying JAR to deploy folder...'
                bat """
                    if not exist %DEPLOY_PATH% mkdir %DEPLOY_PATH%
                    copy /Y bookstore\\target\\%JAR_NAME% %DEPLOY_PATH%\\%JAR_NAME%
                """
            }
        }

        stage('Restart App') {
            steps {
                echo 'Restarting app...'
                bat """
                    for /f "tokens=5" %%a in ('netstat -aon ^| findstr :8080 ^| findstr LISTENING') do taskkill /F /PID %%a
                    start "" java -jar %DEPLOY_PATH%\\%JAR_NAME%
                """
            }
        }
    }
}
