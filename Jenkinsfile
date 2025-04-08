pipeline {
    agent any

    environment {
        DB_IMAGE = "ada26/ktx:db"
        DB_EC2_IP = '54.169.198.193'
        SSH_KEY_DB = credentials('SSH_KEY_DB')
    }

    stages {
        stage('Deploy to EC2') {
            steps {
                script {
                    sh """
                        mkdir -p ~/.ssh
                        echo "${SSH_KEY_DB}" > ~/.ssh/id_rsa
                        chmod 600 ~/.ssh/id_rsa
                        ssh -o StrictHostKeyChecking=no -i ~/.ssh/id_rsa ec2-user@${DB_EC2_IP} "
                            docker pull ${DB_IMAGE}
                            docker stop db-container || true
                            docker rm db-container || true
                            docker rmi \$(docker images --filter=reference='ada26/ktx-db:*' -q | grep -v \$(docker images -q ${DB_IMAGE})) || true
                            docker run -d --name db-container -p 3306:3306 ${DB_IMAGE}
                        "
                        rm -f ~/.ssh/id_rsa
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
