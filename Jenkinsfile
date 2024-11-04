pipeline {
    agent {
        label {
            label 'ubuntu-slave'
            retries 5
        }
    }

    tools {
        // Встановіть версію Maven, налаштовану як "M3", і додайте її до шляху.
        maven "mvn"
    }

    stages {
        stage('Build') {
            steps {
                // Очистити робочу директорію
                cleanWs()
                
                // Завантажити код з репозиторію GitHub
                git branch: 'main', url: 'https://github.com/XadmaX/WildFly-Servlet-Example.git'

                // Запустіть Maven на Unix-агенті.
                sh "mvn clean package"
            }

            post {
                // Якщо Maven зміг запустити тести, навіть якщо деякі тести
                // провалилися, зафіксувати результати тестування та архівувати .war файл.
                success {
                    archiveArtifacts artifacts: 'target/*.war', followSymlinks: false
                }
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['your-ssh-credential-id']) { // Вставте ваш ID SSH-ключа в Jenkins
                    // Копіюємо .war файл на EC2 інстанс
                    sh 'scp -o StrictHostKeyChecking=no target/*.war <ec2_user>@<ec2_instance_ip>:/opt/wildfly/standalone/deployments/'
                    
                    // Перезапустіть WildFly, щоб розгорнути нову версію
                    sh 'ssh -o StrictHostKeyChecking=no <ec2_user>@<ec2_instance_ip> "sudo systemctl restart wildfly"'
                }
            }
        }
    }
}
