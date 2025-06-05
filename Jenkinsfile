pipeline {
    agent any

    environment {
        MIRTH_URL = 'https://192.168.1.6:8443/api/'
        MIRTH_USER = 'admin'
        MIRTHSYNC_PASSWORD = 'admin123' // Use Jenkins credentials for security
        MIRTH_TARGET_DIR = "${WORKSPACE}/Channels"
        MIRTHSYNC_JAR = '/var/jenkins_home/mirthsync-3.1.0-standalone.jar'
    }

    stages {
        stage('Verify Environment') {
            steps {
                script {
                    // Check if MIRTHSYNC_JAR exists and is accessible
                    sh 'ls -l ${MIRTHSYNC_JAR} || echo "MIRTHSYNC_JAR not found"'
                    // List contents of MIRTH_TARGET_DIR before running mirthsync
                    sh 'ls -lR ${MIRTH_TARGET_DIR} || echo "No files in MIRTH_TARGET_DIR"'
                }
            }
        }
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
        stage('Check Files After Push') {
            steps {
                script {
                    // List contents of MIRTH_TARGET_DIR after mirthsync
                    sh 'ls -lR ${MIRTH_TARGET_DIR} || echo "No files in MIRTH_TARGET_DIR after push"'
                }
            }
        }
    }
}