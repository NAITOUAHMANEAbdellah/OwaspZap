

pipeline {
    agent any

    stages {
        
        stage('Clone Repository') {
            steps {
                script {
                    echo '=========== Cloning the repo ================='
                    git url: 'https://github.com/NAITOUAHMANEAbdellah/OwaspZap.git', branch: "main"
                }
            }
        }

        
        stage('Pull Images & Run Containers') {
            steps {
                echo "========== Pulling and running the containers ========"
                sh "docker-compose up -d"
            }
        }   

        
        stage('Scanning target on OWASP container') {
            steps {
                script {
                    scan_type = "${params.SCAN_TYPE ?: 'Baseline'}".trim()
                    target = "${params.TARGET ?: 'http://localhost:8088/'}".trim()
                    echo "Scan type: $scan_type"
                    echo "Target: $target"

                    // Ensure ZAP container is running
                    echo "Starting or restarting ZAP container..."
                    sh '''
                        if [ "$(docker ps -aq -f name=zaproxy)" ]; then
                            docker rm -f zaproxy || true
                        fi
                        docker pull zaproxy/zap-stable
                        docker run -d --name zaproxy -p 8090:8090 zaproxy/zap-stable
                    '''

                    // Wait for the ZAP container to initialize
                    echo "Waiting for ZAP container to initialize..."
                    sleep(60)  // Increase sleep time to 60 seconds

                    // Fetch ZAP container logs to understand why it's failing
                    echo "Fetching ZAP container logs..."
                    sh 'docker logs zaproxy || echo "No logs available for zaproxy container."'

                    // Check if the container is running
                    def isRunning = sh(script: '''
                        docker inspect -f '{{.State.Running}}' zaproxy || echo "false"
                    ''', returnStdout: true).trim()
                    
                    if (isRunning != "true") {
                        error("ZAP container is not running! Check logs for details.")
                    }

                    // Execute the scan based on the chosen scan type
                    echo "Executing ZAP scan: $scan_type..."
                    if (scan_type == 'Baseline') {
                        sh """
                            docker exec zaproxy zap-baseline.py \
                            -t $target \
                            -r /zap/wrk/zap_report_baseline.html \
                            -I
                        """
                    } else if (scan_type == 'APIs') {
                        sh """
                            docker exec zaproxy zap-api-scan.py \
                            -t $target \
                            -r /zap/wrk/zap_report_api.html \
                            -I
                        """
                    } else if (scan_type == 'Full') {
                        sh """
                            docker exec zaproxy zap-full-scan.py \
                            -t $target \
                            -r /zap/wrk/zap_report_full.html \
                            -I
                        """
                    } else {
                        error("Invalid scan type: $scan_type. Must be 'Baseline', 'APIs', or 'Full'.")
                    }

                    // Copy the reports from the container to the Jenkins workspace
                    echo "Copying ZAP reports to workspace..."
                    sh 'docker cp zaproxy:/zap/wrk/. .'

                    // Archive the reports
                    echo "Archiving ZAP reports..."
                    archiveArtifacts artifacts: '*.html', onlyIfSuccessful: true
                }
            }
        }

    }

    post {
        always {
            // Cleaning up the workspace
            script {
                echo "============= Turning OFF containers ==============="
                sh 'docker-compose down'
                echo "===== LOG: docker-compose exit-code2 ====="

                echo "============= Cleaning up the workspace ==============="
                sh 'rm -rf * .git'
            }
        }
    }
}




