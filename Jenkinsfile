pipeline {
    agent {
        label {
            label 'ubuntu-slave' // Замініть на вашого агента, якщо потрібно
            retries 5
        }
    }

    tools {
        maven "mvn" // Переконайтеся, що ви налаштували Maven у Jenkins
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
                    // Архівувати артефакти, які включають ваш .war файл
                    archiveArtifacts artifacts: 'target/*.war', followSymlinks: false
                }
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['your-ssh-credential-id']) { // Вставте ваш ID SSH-ключа в Jenkins
                    // Копіюємо .war файл на EC2 інстанс
                    sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@13.48.135.73:/opt/wildfly/standalone/deployments/'

                    // Перезапустіть WildFly
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@13.48.135.73 "sudo systemctl restart wildfly"'
                }
            }
        }
    }
}
