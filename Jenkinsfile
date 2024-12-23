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


        // stage('Run OWASP ZAP Scan') {
        //     steps {
        //         script {
        //             echo "=========== Running OWASP ZAP scan ================"
        //             sh '''
        //             docker run --rm \
        //                 -v $(pwd):/zap/wrk:rw \
        //                 zaproxy/zap-stable zap-baseline.py \
        //                 -t http://localhost:8088 \
        //                 -r zap_report.html
        //             '''
        //         }
        //     }
        // }




           

//        stage('Scanning target on OWASP container') {
//     steps {
//         script {
//             // Retrieve scan type and target from parameters
//             scan_type = "${params.SCAN_TYPE}"
//             target = "${params.TARGET}"
//             echo "----> scan_type: $scan_type"
//             echo "----> target: $target"

//             // Ensure ZAP container is running (restart if needed)
//             sh 'docker start zaproxy || docker run -d --name zaproxy -p 8090:8090 zaproxy/zap-stable'

//             // Wait for the ZAP container to be fully up and running
//             sleep(5)

//             // Execute different scans based on the chosen scan type
//             if (scan_type == 'Baseline') {
//                 echo "Running Baseline scan..."
//                 sh """
//                      docker exec zaproxy zap-baseline.py \
//                      -t $target \
//                      -r zap_report_baseline.html \
//                      -I
//                  """
//             } else if (scan_type == 'APIs') {
//                 echo "Running API scan..."
//                 sh """
//                      docker exec zaproxy zap-api-scan.py \
//                      -t $target \
//                      -r zap_report_api.html \
//                      -I
//                  """
//             } else if (scan_type == 'Full') {
//                 echo "Running Full scan..."
//                 sh """
//                      docker exec zaproxy zap-full-scan.py \
//                      -t $target \
//                      -r zap_report_full.html \
//                      -I
//                  """
//             } else {
//                 echo 'Invalid scan type. Please select either Baseline, APIs, or Full.'
//             }


//             // Debug: List files in the ZAP container's report directory
//             echo "Listing files in ZAP report directory:"
//             sh 'docker exec zaproxy ls -al /zap/wrk/'
               
//             // Copy reports from the ZAP container to the Jenkins workspace
//             echo "Copying ZAP report to workspace..."
//             sh 'docker cp zaproxy:/zap/wrk/. .'

//             // Archive the generated report for Jenkins
//             echo "Archiving ZAP report..."
//             archiveArtifacts artifacts: '*.html', onlyIfSuccessful: true
               
//         }
//     }
// }


           ////////////////////////////////////////////////////////////////
       stage('Scanning target on OWASP container') {
    steps {
        script {
            scan_type = "${params.SCAN_TYPE ?: 'Baseline'}".trim()
            target = "${params.TARGET ?: 'https://www.almussa3id.com/'}".trim()
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
            sleep(20)

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


              /////////////////////////////////////////////////////////////////////


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
