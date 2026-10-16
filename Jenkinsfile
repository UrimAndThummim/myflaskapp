pipeline {
    agent any

    environment {
        // 注意：替换为你自己电脑上的 python.exe 完整路径（可用 where python 查看）
        PYTHON_PATH = 'C:\\Users\\mr\\AppData\\Local\\Programs\\Python\\Python313\\python.exe'
    }

    stages {
        stage('Verify Python Path') {
            steps {
                bat """
                    @echo off
                    echo === 验证Python路径及版本 ===
                    ${PYTHON_PATH} --version
                    echo Python路径验证通过！
                """
            }
        }

        stage('Checkout') {
            steps {
                // 注意：替换为你自己的仓库地址
                git url: 'https://github.com/UrimAndThummim/myflaskapp.git', branch: 'main'
            }
        }

        stage('Fix pip') {
            steps {
                bat """
                    @echo off
                    echo === 修复pip环境 ===
                    ${PYTHON_PATH} -m ensurepip --upgrade
                    ${PYTHON_PATH} -m pip --version
                """
            }
        }

        stage('Install Dependencies') {
            steps {
                bat """
                    @echo off
                    ${PYTHON_PATH} -m pip install --upgrade pip
                    ${PYTHON_PATH} -m pip install -r requirements.txt
                """
            }
        }

        stage('Lint') {
            steps {
                bat "${PYTHON_PATH} -m pip install flake8 && ${PYTHON_PATH} -m flake8 app.py"
            }
        }

        stage('Test') {
            steps {
                bat """
                    ${PYTHON_PATH} -m pip install pytest
                    ${PYTHON_PATH} -m pytest --cov=app tests/ --cov-report=html
                """
            }
            post {
                always {
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'htmlcov',
                        reportFiles: 'index.html',
                        reportName: 'Coverage Report'
                    ])
                }
            }
        }

        stage('Build') {
            steps {
                bat """
                    ${PYTHON_PATH} -m pip install pyinstaller
                    ${PYTHON_PATH} -m PyInstaller --onefile app.py
                """
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                // 启动Flask应用（按需调整）
                bat "start ${PYTHON_PATH} app.py"
            }
        }
    }

    post {
        success { echo 'CI/CD pipeline completed successfully!' }
        failure { echo 'CI/CD pipeline failed!' }
    }
}
