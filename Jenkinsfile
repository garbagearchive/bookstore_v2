pipeline {
    agent any

    environment {
        JAR_NAME = 'bookstore-0.0.1-SNAPSHOT.jar'
        DEPLOY_DIR = 'C:\\deploy\\springboot\\bookstore'
    }

    stages {

        stage('Clone') {
            steps {
                echo 'Cloning source code...'
                git branch: 'main', url: 'https://github.com/garbagearchive/bookstore_v2'
            }
        }

        stage('Clean') {
            steps {
                echo 'Cleaning previous builds...'
                bat 'mvn clean'
            }
        }

        stage('Restore Dependencies') {
            steps {
                echo 'Downloading dependencies...'
                bat 'mvn dependency:resolve'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the Spring Boot project...'
                bat 'mvn package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                bat 'mvn test'
            }
        }

        stage('Publish') {
            steps {
                echo 'Copy JAR to deploy folder...'

                // Tạo thư mục deploy nếu chưa có
                bat '''
                if not exist "%DEPLOY_DIR%" (
                    mkdir "%DEPLOY_DIR%"
                )
                xcopy /Y /I /R /E "target\\%JAR_NAME%" "%DEPLOY_DIR%"
                '''
            }
        }

        stage('Run App') {
            steps {
                echo 'Starting Spring Boot application...'

                // Dừng tiến trình cũ nếu đang chạy
                bat '''
                FOR /F "tokens=5" %%P IN ('netstat -aon ^| find ":8080" ^| find "LISTENING"') DO taskkill /F /PID %%P
                '''

                // Chạy ứng dụng (background)
                bat '''
                start "" java -jar "%DEPLOY_DIR%\\%JAR_NAME%"
                '''
            }
        }
    }
}
