pipeline {
    agent {
        label {
            label 'ubuntu-slave'
            retries 5
        }
    }

    tools {
        maven "mvn"
    }

    stages {
        stage('Build') {
            steps {
                cleanWs() // Очистити робочу директорію

                // Завантажити код з репозиторію GitHub
                git branch: 'main', url: 'https://github.com/XadmaX/WildFly-Servlet-Example.git'

                // Запустіть Maven для збору проекту
                sh "mvn clean package"
            }

            post {
                success {
                    /
                    archiveArtifacts artifacts: 'target/*.war', followSymlinks: false
                }
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['key-03b31388818553582']) { 
                    // Копіюємо .war файл на EC2 інстанс
                    sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@13.48.135.73:/opt/wildfly/standalone/deployments/'

                    // Перезапустіть WildFly
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@13.48.135.73 "sudo systemctl restart wildfly"'
                }
            }
        }
    }
}
