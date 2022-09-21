pipeline {
    agent any

    stages {
        stage('pull code') {
            steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/${branch}']], extensions: [], userRemoteConfigs: [[credentialsId: '06612214-df29-49c3-a66f-3a87df1f042e', url: 'http://172.16.11.29:8080/familiar/web_demo.git']]])
            }
        }
        stage('code checking') {
            steps {
                script {
                    //引入SonarQubeScanner工具
                    scannerHome = tool 'SQ Scanner'
                }
                //引入SonarQube的服务器环境
                withSonarQubeEnv('SonarQube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
        stage('build project') {
            steps {
                sh ' /usr/local/maven/bin/mvn clean package'
            }
        }
        stage('publish project') {
            steps {
                deploy adapters: [tomcat9(credentialsId: '0b0f0dda-086d-4a26-ba21-b75745e15d56', path: '', url: 'http://172.16.11.29:8082')], contextPath: null, war: 'target/*.war'
            }
        }
        
    }
        post {
            always {
                emailext(
                subject: '构建通知：${PROJECT_NAME} - Build # ${BUILD_NUMBER} -${BUILD_STATUS}!',
                body: '${FILE,path="email.html"}',
                to: '1969185869@qq.com'
            )
        }
    }
}
