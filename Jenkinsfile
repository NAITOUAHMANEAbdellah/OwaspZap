// pipeline {
//     agent any
//     stages {
       
//         stage('Clone Repository') {
//             steps {
//                 script {
//                     echo '===========Cloning the repo================='
//                     git url: 'https://github.com/NAITOUAHMANEAbdellah/OwaspZap.git', branch: "main"      
//                 }
//             }
//         }

//         stage('Pull images & Run Containers') {
//             steps {
//                 // Start the services using Docker Compose
//                 echo "==========pulling and running the containers======="
//                 //sh 'IF EXIST docker-compose.yml echo FOUND'
//                 sh "docker-compose up -d"
//                 echo "=====LOG====exit-code1: %ERRORLEVEL%"

//             }
//         }
       
//         // stage('Scan Containers with Trivy') {
//         //     steps {
//         //         script {
//         //             echo "==========Scanning the images ======="
                    
//         //             // Display Trivy version
//         //             sh "trivy --version"

//         //             //show retrieved data on the runtime 
//         //             //sh "docker-compose config"
        
//         //             // List of services to scan 
//         //             def services = ['akaunting', 'akaunting-db']
                    
//         //             // Loop through services to scan each image
//         //             for (service in services) {
//         //                 // Retrieve the image ID using docker-compose and store it in a variable
//         //                 def imageId = sh(script: "docker-compose images ${service} -q", returnStdout: true).trim()
//         //                 def imageId_trimmed = imageId.readLines().last().trim()

//         //                 if (imageId_trimmed) {
//         //                     echo "Scanning image for service: ${service} :::: ${imageId_trimmed}"
                            
//         //                     // this line below works  
//         //                     // info it trivy is fixing vuln stuff
//         //                     def scanResult = sh(script: "trivy image --light --severity CRITICAL,HIGH --format json -o D:\\Desktop\\${service}_scan_report.json ${imageId_trimmed}", returnStdout: true)
//         //                     echo "===============Scan-Report-file--->\tD:\\Desktop\\${service}_scan_report.json\t========"
                            
//         //                     // Run Trivy scan containers and save report on a file for each image
//         //                     // sh 'trivy -q image --light --severity CRITICAL,HIGH --format json -o "D:\\Desktop\\${service}_scan_report.json" ${imageId_trimmed}'
//         //                 } else {
//         //                     echo "No image found for service: ${service}"
//         //                 }
//         //             }
//         //         }
//         //     }
//         // }

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
stage('Scanning target on local OWASP ZAP instance') {
    steps {
        script {
            scan_type = "${params.SCAN_TYPE ?: 'Baseline'}".trim()
            target = "${params.TARGET ?: 'http://localhost:8088/'}".trim()
            echo "Scan type: $scan_type"
            echo "Target: $target"

            // Ensure the local ZAP instance is running
            echo "Checking if local ZAP instance is running on port 8090..."
            def isZAPRunning = sh(script: 'netstat -an | grep 8090 || echo "false"', returnStdout: true).trim()
            
            if (isZAPRunning == "false") {
                error("OWASP ZAP is not running on port 8090. Please start the ZAP application.")
            }

            // Execute the scan based on the chosen scan type
            echo "Executing ZAP scan: $scan_type..."
            if (scan_type == 'Baseline') {
                sh """
                    curl -s -X POST http://localhost:8090/JSON/ascan/action/scan \
                    --data-urlencode "url=$target" \
                    --data-urlencode "scanPolicyName=Default Policy" \
                    --data-urlencode "maxUrlCount=5"
                """
            } else if (scan_type == 'APIs') {
                sh """
                    curl -s -X POST http://localhost:8090/JSON/ascan/action/scan \
                    --data-urlencode "url=$target" \
                    --data-urlencode "scanPolicyName=API Policy" \
                    --data-urlencode "maxUrlCount=5"
                """
            } else if (scan_type == 'Full') {
                sh """
                    curl -s -X POST http://localhost:8090/JSON/ascan/action/scan \
                    --data-urlencode "url=$target" \
                    --data-urlencode "scanPolicyName=Full Scan Policy" \
                    --data-urlencode "maxUrlCount=10"
                """
            } else {
                error("Invalid scan type: $scan_type. Must be 'Baseline', 'APIs', or 'Full'.")
            }

            // Fetch ZAP scan report (you can adjust the path as needed)
            echo "Fetching ZAP scan report..."
            sh 'curl -s http://localhost:8090/JSON/core/view/alerts'

            // Archive the reports
            echo "Archiving ZAP reports..."
            archiveArtifacts artifacts: '*.html', onlyIfSuccessful: true
        }
    }
}
}
post {
    always {
        // Optional: Clean up workspace (if necessary)
        script {
            echo "============= Cleaning up the workspace ==============="
            sh 'rm -rf * .git'
        }
    }
}
}
