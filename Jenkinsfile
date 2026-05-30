pipeline {
    agent any

    tools {
        // 必须和你在 Jenkins「全局工具配置」里面配置的 Maven 名字一模一样
        maven 'local-maven' 
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Maven Build & Test') {
            steps {
                // Windows 环境下使用 bat
                bat 'mvn clean compile test pmd:pmd javadoc:javadoc package'
            }
        }
    }

    post {
        always {
            // 存档打包出来的 jar 包
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: false
            // 收集 JUnit 测试报告
            junit '**/target/surefire-reports/*.xml'
            // 收集并展示 JavaDoc 文档
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'target/site/apidocs', reportFiles: 'index.html', reportName: 'JavaDoc'])
        }
    }
}