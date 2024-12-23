pipeline {
    agent any
    stages {
       
        stage('Clone Repository') {
            steps {
                script {
                    echo '===========Cloning the repo================='
                    git url: 'https://github.com/NAITOUAHMANEAbdellah/OwaspZap.git', branch: "main"      
                }
            }
        }

        stage('Pull images & Run Containers') {
            steps {
                // Start the services using Docker Compose
                echo "==========pulling and running the containers======="
                //sh 'IF EXIST docker-compose.yml echo FOUND'
                sh "docker-compose up -d"
                echo "=====LOG====exit-code1: %ERRORLEVEL%"

            }
        }
       
        // stage('Scan Containers with Trivy') {
        //     steps {
        //         script {
        //             echo "==========Scanning the images ======="
                    
        //             // Display Trivy version
        //             sh "trivy --version"

        //             //show retrieved data on the runtime 
        //             //sh "docker-compose config"
        
        //             // List of services to scan 
        //             def services = ['akaunting', 'akaunting-db']
                    
        //             // Loop through services to scan each image
        //             for (service in services) {
        //                 // Retrieve the image ID using docker-compose and store it in a variable
        //                 def imageId = sh(script: "docker-compose images ${service} -q", returnStdout: true).trim()
        //                 def imageId_trimmed = imageId.readLines().last().trim()

        //                 if (imageId_trimmed) {
        //                     echo "Scanning image for service: ${service} :::: ${imageId_trimmed}"
                            
        //                     // this line below works  
        //                     // info it trivy is fixing vuln stuff
        //                     def scanResult = sh(script: "trivy image --light --severity CRITICAL,HIGH --format json -o D:\\Desktop\\${service}_scan_report.json ${imageId_trimmed}", returnStdout: true)
        //                     echo "===============Scan-Report-file--->\tD:\\Desktop\\${service}_scan_report.json\t========"
                            
        //                     // Run Trivy scan containers and save report on a file for each image
        //                     // sh 'trivy -q image --light --severity CRITICAL,HIGH --format json -o "D:\\Desktop\\${service}_scan_report.json" ${imageId_trimmed}'
        //                 } else {
        //                     echo "No image found for service: ${service}"
        //                 }
        //             }
        //         }
        //     }
        // }

    }
    post {
        always {
            // Cleaning up the workspace
            script {
                
                echo "=============turning OFF containers==============="
                sh 'docker-compose down'
                echo "=====LOG====docker-compose-exit-code2: %ERRORLEVEL%"
                
                echo "=============cleaning up the workspace==============="
                sh 'del /q /s * && for /d %%p in (*) do rmdir "%%p" /s /q'
                
                echo "=============removing the .git folder==============="
                sh 'rmdir /s /q .git'
            }
        }
    } 

}
