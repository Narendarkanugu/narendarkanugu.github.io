pipeline {
    agent any

    environment {
        DEPLOY_DIR = "/var/www/html/my-site"
        REPO_URL = "https://github.com/Narendarkanugu/narendarkanugu.github.io.git"
    }

    stages {
        stage('Clone GitHub Repo') {
            steps {
                sh '''
                sudo rm -rf $DEPLOY_DIR
                sudo git clone $REPO_URL $DEPLOY_DIR
                sudo chown -R www-data:www-data $DEPLOY_DIR
                '''
            }
        }

        stage('Configure Nginx on Port 8080') {
            steps {
                sh '''
                CONFIG="/etc/nginx/sites-available/my-site"

                sudo bash -c "cat > $CONFIG" <<EOF
server {
    listen 8080;
    server_name _;

    root $DEPLOY_DIR;
    index index.html;

    location / {
        try_files \$uri \$uri/ =404;
    }
}
EOF

                sudo ln -sf $CONFIG /etc/nginx/sites-enabled/my-site
                sudo nginx -t && sudo systemctl reload nginx
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployed at http://<your-ec2-ip>:8080"
        }
        failure {
            echo "❌ Deployment failed. Check logs."
        }
    }
}
