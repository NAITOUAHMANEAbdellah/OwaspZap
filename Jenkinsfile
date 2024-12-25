

// pipeline {
//     agent any

//     stages {
        
//         stage('Clone Repository') {
//             steps {
//                 script {
//                     echo '=========== Cloning the repo ================='
//                     git url: 'https://github.com/NAITOUAHMANEAbdellah/OwaspZap.git', branch: "main"
//                 }
//             }
//         }

        
//         stage('Pull Images & Run Containers') {
//             steps {
//                 echo "========== Pulling and running the containers ========"
//                 sh "docker-compose up -d"
//             }
//         }   

        
//         stage('Scanning target on OWASP container') {
//             steps {
//                 script {
//                     scan_type = "${params.SCAN_TYPE ?: 'Baseline'}".trim()
//                     target = "${params.TARGET ?: 'http://localhost:8088/'}".trim()
//                     echo "Scan type: $scan_type"
//                     echo "Target: $target"

//                     // Ensure ZAP container is running
//                     echo "Starting or restarting ZAP container..."
//                     sh '''
//                         if [ "$(docker ps -aq -f name=zaproxy)" ]; then
//                             docker rm -f zaproxy || true
//                         fi
//                         docker pull zaproxy/zap-stable
//                         docker run -d --name zaproxy -p 8090:8090 zaproxy/zap-stable
//                     '''

//                     // Wait for the ZAP container to initialize
//                     echo "Waiting for ZAP container to initialize..."
//                     sleep(60)  // Increase sleep time to 60 seconds

//                     // Fetch ZAP container logs to understand why it's failing
//                     echo "Fetching ZAP container logs..."
//                     sh 'docker logs zaproxy || echo "No logs available for zaproxy container."'

//                     // Check if the container is running
//                     def isRunning = sh(script: '''
//                         docker inspect -f '{{.State.Running}}' zaproxy || echo "false"
//                     ''', returnStdout: true).trim()
                    
//                     if (isRunning != "true") {
//                         error("ZAP container is not running! Check logs for details.")
//                     }

//                     // Execute the scan based on the chosen scan type
//                     echo "Executing ZAP scan: $scan_type..."
//                     if (scan_type == 'Baseline') {
//                         sh """
//                             docker exec zaproxy zap-baseline.py \
//                             -t $target \
//                             -r /zap/wrk/zap_report_baseline.html \
//                             -I
//                         """
//                     } else if (scan_type == 'APIs') {
//                         sh """
//                             docker exec zaproxy zap-api-scan.py \
//                             -t $target \
//                             -r /zap/wrk/zap_report_api.html \
//                             -I
//                         """
//                     } else if (scan_type == 'Full') {
//                         sh """
//                             docker exec zaproxy zap-full-scan.py \
//                             -t $target \
//                             -r /zap/wrk/zap_report_full.html \
//                             -I
//                         """
//                     } else {
//                         error("Invalid scan type: $scan_type. Must be 'Baseline', 'APIs', or 'Full'.")
//                     }

//                     // Copy the reports from the container to the Jenkins workspace
//                     echo "Copying ZAP reports to workspace..."
//                     sh 'docker cp zaproxy:/zap/wrk/. .'

//                     // Archive the reports
//                     echo "Archiving ZAP reports..."
//                     archiveArtifacts artifacts: '*.html', onlyIfSuccessful: true
//                 }
//             }
//         }

//     }

//     post {
//         always {
//             // Cleaning up the workspace
//             script {
//                 echo "============= Turning OFF containers ==============="
//                 sh 'docker-compose down'
//                 echo "===== LOG: docker-compose exit-code2 ====="

//                 echo "============= Cleaning up the workspace ==============="
//                 sh 'rm -rf * .git'
//             }
//         }
//     }
// }


pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                script {
                    echo '===========Cloning the repository================='
                    git url: 'https://github.com/sam99235/akaunting_docker_app.git', branch: 'main'
                }
            }
        }

        stage('Pull Images & Run Containers') {
            steps {
                // Start the services using Docker Compose
                echo '==========Pulling images and running the containers======='
                sh 'docker-compose up -d'
                echo '=====LOG==== Exit code for Docker Compose: $?'
            }
        }

        stage('Scan Security with OWASP ZAP') {
            steps {
                script {
                    echo '==========Starting OWASP ZAP security scan=========='

                    // List of services to scan
                    def services = ['akaunting', 'akaunting-db']

                    // Loop through services and scan with OWASP ZAP
                    for (service in services) {
                        // Retrieve the container ID for the service
                        def containerId = sh(script: "docker-compose ps -q ${service}", returnStdout: true).trim()

                        if (containerId) {
                            echo "Scanning container for service: ${service} :::: ${containerId}"

                            // Run OWASP ZAP scan against the container and generate the report
                            sh "zap-cli quick-scan --self-contained -t ${containerId} -r OWASP_ZAP_Report_${service}.html"
                            echo "===========OWASP ZAP Scan Completed for ${service}. Report: OWASP_ZAP_Report_${service}.html============="
                        } else {
                            echo "No container found for service: ${service}"
                        }
                    }
                }
            }
        }

        stage('Scan Dependencies with Snyk') {
            steps {
                script {
                    echo '==========Starting Snyk vulnerability scan=========='

                    // Ensure Snyk CLI is installed and authenticated
                    sh 'snyk auth b34fed82-c158-402f-b71d-41fa6155ef44' // Replace with your actual Snyk token, found in your Snyk account settings

                    // Loop through services to scan their images
                    def services = ['akaunting', 'akaunting-db']

                    for (service in services) {
                        // Retrieve the image ID for the service
                        def imageId = sh(script: "docker-compose images ${service} -q", returnStdout: true).trim()

                        if (imageId) {
                            echo "Scanning image for service: ${service} :::: ${imageId}"

                            // Run Snyk scan against the image and save the results
                            sh "snyk test --docker ${imageId} --json > Snyk_Report_${service}.json"
                            echo "===========Snyk Scan Completed for ${service}. Report: Snyk_Report_${service}.json============="
                        } else {
                            echo "No image found for service: ${service}"
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                echo '=============Shutting down containers==============='
                sh 'docker-compose down'
                echo '=====LOG==== Exit code for Docker Compose Down: $?'

                echo '=============Cleaning up workspace==============='
                sh 'rm -rf *'

                echo '=============Removing the .git folder==============='
                sh 'rm -rf .git'
            }
        }
    }
}


