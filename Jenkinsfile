pipeline {
    agent any
    
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
                
                // Tự động cài đặt Node.js phiên bản 20 vào môi trường Jenkins nếu chưa có
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
                    
                    if ! command -v node &> /dev/null
                    then
                        echo "Đang cài đặt Node.js 20..."
                        curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
                        apt-get update && apt-get install -y nodejs
                    fi
                    
                    node -v
                    npm -v
                    npm ci
                    npm run build
                '''
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
