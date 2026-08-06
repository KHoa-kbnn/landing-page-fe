pipeline {
    agent any
    
    options {
        timestamps()
        timeout(time: 15, unit: 'MINUTES')
    }
environment {
        // Đổi đường dẫn deploy vào thư mục local trong workspace thay vì /var/www
        DEPLOY_DIR = "${env.WORKSPACE}/dist_output"
        NODE_ENV   = "production"
    }
    stages {
        stage('Build') {
            steps {
                echo "Đang build trên nhánh ${env.BRANCH_NAME}"
                
                // Tự động tải và cài đặt Node.js cục bộ vào thư mục workspace (Không cần quyền root)
                sh '''
                    export NODE_VERSION="20.11.0"
                    export ARCH="x64"
                    export PREFIX="$WORKSPACE/node_custom"
                    export PATH="$PREFIX/bin:$PATH"
                    
                    if [ ! -d "$PREFIX/bin" ]; then
                        echo "Đang tải Node.js v${NODE_VERSION}..."
                        mkdir -p "$PREFIX"
                        curl -O https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-linux-x64.tar.gz
                        tar -xzf node-v${NODE_VERSION}-linux-x64.tar.gz -C "$PREFIX" --strip-components=1
                        rm node-v${NODE_VERSION}-linux-x64.tar.gz
                    fi
                    
                    # Đưa Node vào biến môi trường của tiến trình hiện tại
                    export PATH="$WORKSPACE/node_custom/bin:$PATH"
                    
                    node -v
                    npm -v
                    
                    npm install
                    npm run build
                '''
            }
        }
        stage('Test') {
            steps {
                sh '''
                    export PATH="$WORKSPACE/node_custom/bin:$PATH"
                    npm test
                '''
            }
        }
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo "Deploy sang server smac@192.168.123.101 thông qua Ansible"
                sshagent(credentials: ['ansible-ssh-key']) {
                    sh '''
                        cd ansible
                        export ANSIBLE_HOST_KEY_CHECKING=false
                        ansible --version
                        ansible-playbook -i inventory site.yml --diff
                    '''
                }
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
