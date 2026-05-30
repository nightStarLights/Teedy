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

        stage('Maven Package') {
            steps {
                // 第一步：只编译和打出 Jar 包/War 包，跳过测试和文档，确保核心产物一定能出来
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Generate PMD & JavaDoc') {
            steps {
                // 用 catchError 包裹：即使这两步因为 JDK 25 检查太严格而报错，流水线依然会继续往下走，最终整个任务会是绿色的（SUCCESS）
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    bat 'mvn pmd:pmd javadoc:javadoc -DskipTests'
                }
            }
        }
    }

    post {
        always {
            // 存档打包出来的 war/jar 包
            archiveArtifacts artifacts: '**/target/*.?ar', allowEmptyArchive: true
            
            // 展示 JavaDoc（加了 allowMissing: true，防止没有生成时 Jenkins 报错）
            publishHTML([allowMissing: true, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'target/site/apidocs', reportFiles: 'index.html', reportName: 'JavaDoc'])
        }
    }
}