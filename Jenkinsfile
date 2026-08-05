pipeline {
    agent any
    tools {
        nodejs 'Node20'
    }
    options {
        timestamps()
        timeout(time: 15, unit: 'MINUTES')
    }
    environment {
        DEPLOY_DIR = "/var/www/demo-app"
        NODE_ENV   = "production"
    }
    stages {
        stage('Build') {
            steps {
                echo "Đang build trên nhánh ${env.BRANCH_NAME}"
                sh 'npm ci'
                sh 'npm run build'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo "Deploy sang ${DEPLOY_DIR}"
                sh '''
                    mkdir -p "$DEPLOY_DIR"
                    cp -r dist/* "$DEPLOY_DIR"/
                '''
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'dist/**', allowEmptyArchive: true, fingerprint: true
        }
        success {
            echo "Pipeline THÀNH CÔNG trên nhánh ${env.BRANCH_NAME}"
        }
        failure {
            echo "Pipeline THẤT BẠI — kiểm tra log stage vừa lỗi"
        }
    }
}