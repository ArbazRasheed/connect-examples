pipeline {
    agent any

    environment {
        MIRTH_URL = 'https://192.168.1.6:8443/api/'
        MIRTH_USER = 'admin'
        MIRTHSYNC_PASSWORD = 'admin123' // Use Jenkins credentials for security
        MIRTH_TARGET_DIR = "${WORKSPACE}"
        MIRTHSYNC_JAR = '/var/jenkins_home/mirthsync-3.1.0-standalone.jar' // Adjusted for Linux path
    }

    stages {
        stage('Push to Mirth') {
            steps {
                script {
                    try {
                        sh """
                            java -jar "${env.MIRTHSYNC_JAR}" \
                                -s "${env.MIRTH_URL}" \
                                -u "${env.MIRTH_USER}" \
                                -p "${env.MIRTHSYNC_PASSWORD}" \
                                -t "${env.MIRTH_TARGET_DIR}" \
                                -m items \
                                -d \
                                -i \
                                -v \
                                push
                        """
                        echo 'Pushed and deployed configurations to Mirth'
                    } catch (Exception e) {
                        error "Push to Mirth failed: ${e.message}"
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Archiving artifacts from ${env.MIRTH_TARGET_DIR}"
            archiveArtifacts artifacts: "${env.MIRTH_TARGET_DIR}/**", allowEmptyArchive: true
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}