pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/CloudGeniuses/Boardgame.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }
        
        stage('Test') {
            steps {
                sh "mvn test"
            }
        }
        
        // stage('File System Scan') {
        //     steps {
        //         sh "trivy fs --format table -o trivy-fs-report.html ."
        //     }
        // }
        
        // stage('SonarQube Analysis') {
        //     steps {
        //         withSonarQubeEnv('sonar') {
        //             sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=BoardGame -Dsonar.projectKey=BoardGame \
        //                     -Dsonar.java.binaries=. '''
        //         }
        //     }
        // }
        
        // stage('Quality Gate') {
        //     steps {
        //         script {
        //           waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token' 
        //         }
        //     }
        // }
        
        stage('Build') {
            steps {
                sh "mvn package"
            }
        }
        
        // Building Docker images
        // stage('Building image') {
        //     steps {
        //         script {
        //             dockerimage = docker.build registry
        //         }
        //     }
        // }
        
        // Uploading Docker images into Docker Hub
        // stage('Uploading to ECR') {
        //     steps {
        //         script {
        //             sh 'aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 061051256730.dkr.ecr.us-east-2.amazonaws.com'
        //             sh 'docker push 061051256730.dkr.ecr.us-east-2.amazonaws.com/nikhilkhariya40/boardshack:latest'
        //         }
        //     }
        // }
        
        stage('Publish To Nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'isaac', jdk: 'jdk17', maven: 'maven', mavenSettingsConfig: '', traceability: true) {
                    sh "mvn deploy"
                }
            }
         }
        
        // stage('Build Docker Image') {
        //     steps {
        //         script {
        //             echo "Building Docker image: ${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"
        //             // Build the Docker image using the Dockerfile
        //             sh 'docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_TAG} .'
        //         }
        //     }
        // }
        
        // stage('Login to DockerHub') {
        //     steps {
        //         script {
        //             echo "Logging into DockerHub..."
        //             // Login to DockerHub using credentials from Jenkins
        //             withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS_ID}", usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
        //                 sh 'docker login -u ${DOCKERHUB_USER} -p ${DOCKERHUB_PASS}'
        //             }
        //         }
        //     }
        // }
        
        // stage('Docker Image Scan') {
        //     steps {
        //         sh "trivy image --format table -o trivy-image-report.html nikhilkhariya40/boardshack:latest "
        //     }
        // }
        
        // stage('Deploy To Kubernetes') {
        //     steps {
        //         withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.8.146:6443') {
        //             sh "kubectl apply -f deployment-service.yaml"
        //         }
        //     }
        // }
        
        // stage('Verify the Deployment') {
        //     steps {
        //         withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.8.146:6443') {
        //             sh "kubectl get pods -n webapps"
        //             sh "kubectl get svc -n webapps"
        //         }
        //     }
        // }
    }
    
    post {
        always {
            script {
                def jobName = env.JOB_NAME
                def buildNumber = env.BUILD_NUMBER
                def pipelineStatus = currentBuild.result ?: 'UNKNOWN'
                def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red'

                def body = """
                    <html>
                    <body>
                    <div style="border: 4px solid ${bannerColor}; padding: 10px;">
                    <h2>${jobName} - Build ${buildNumber}</h2>
                    <div style="background-color: ${bannerColor}; padding: 10px;">
                    <h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3>
                    </div>
                    <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
                    </div>
                    </body>
                    </html>
                """

                emailext (
                    subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}",
                    body: body,
                    to: 'nikhilkhariya40@gmail.com',
                    from: 'jenkins@example.com',
                    replyTo: 'jenkins@example.com',
                    mimeType: 'text/html',
                    attachmentsPattern: 'trivy-image-report.html'
                )
            }
        }
    }
}
