pipeline {
    agent any

    environment {
        APP_DIR = 'src'
        // SONARQUBE_SERVER = 'https://sonarqube.techworldplus.xyz/' // Jenkins SonarQube server name
        SONARQUBE_SERVER = 'jenkins-sonar' // Jenkins SonarQube server name
        SONAR_PROJECT_KEY = 'sonar-angular'
        SONAR_PROJECT_NAME = 'angular-test'
        SONAR_PROJECT_VERSION = '1.0'
        DOCKER_IMAGE = 'brent/angular-test-app:latest'
        NEXUS_URL = 'nexus.techworldplus.xyz'
        NEXUS_REPO = 'spa-apps'
        NEXUS_CREDENTIALS_ID = 'nexusCreds'
        TRIVY_SEVERITY = 'HIGH,CRITICAL' // Scan for High and Critical vulnerabilities

    }

    stages {
        stage('Install Dependencies') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    npm ci
                    ls -la
                '''
            }
        }

        // stage('Run Tests') {
        //     agent {
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         sh '''
        //             npm test -- --watchAll=false
        //         '''
        //     }
        // }


        // stage('SonarQube Scan') {
        //     agent {
        //         docker {
        //             image 'sonarsource/sonar-scanner-cli'
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         withSonarQubeEnv(SONARQUBE_SERVER) {
        //                 sh '''
        //                     sonar-scanner -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
        //                         -Dsonar.projectName=${SONAR_PROJECT_NAME} \
        //                         -Dsonar.sources=./ \
        //                         -Dsonar.working.directory=.scannerwork
        //                 '''
        //         }
        //     }
        // }





        // stage('Publish SonarQube Artifacts to Nexus') {
        //     steps {
        //         script {
        //             // Archive the SonarQube scan artifacts
        //             // archiveArtifacts artifacts: '**/.scannerwork/**', allowEmptyArchive: true

        //             // Publish the scan reports to Nexus
        //             nexusArtifactUploader(
        //                 nexusVersion: 'nexus3',
        //                 protocol: 'https',
        //                 nexusUrl: "${NEXUS_URL}",
        //                 // groupId: 'com.yourcompany.reactapp',
        //                 // version: "${SONAR_PROJECT_VERSION}",
        //                 repository: "${NEXUS_REPO}",
        //                 credentialsId: "${NEXUS_CREDENTIALS_ID}",
        //                 artifacts: [
        //                     [
        //                         artifactId: 'sonarqube-report',
        //                         classifier: '',
        //                         file: ".scannerwork/report-task.txt",
        //                         type: 'txt'
        //                     ]
        //                 ]
        //             )
        //         }
        //     }
        // }

        // stage('Trivy Scan') {
        //     agent {
        //         docker {
        //             image 'docker.io/aquasec/trivy:latest'
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         sh '''
        //             image python:3.4-alpine
        //         '''
        //     }
        // }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image using the Dockerfile in the app directory
                    docker.build("${DOCKER_IMAGE}", ".")
                }
            }
        }

        stage('Trivy Scan Docker Image') {
            steps {
                script {
                    // Run Trivy scan on the Docker image
                    docker.image('aquasec/trivy:latest').inside {
                        sh "trivy image --severity ${TRIVY_SEVERITY} --exit-code 1 --no-progress ${DOCKER_IMAGE}"
                    }
                }
            }
        }

    }

    post {
        always {
            cleanWs() // Clean workspace after the build
        }
    }
}