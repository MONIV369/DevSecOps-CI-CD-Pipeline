pipeline {

    agent any


    environment {

        IMAGE_NAME = "moniv369/linux-tweet-app"

        IMAGE_TAG = "${BUILD_NUMBER}"

        SONAR_SCANNER_HOME = tool 'SonarScanner'

    }


    stages {


        stage('Checkout Code') {

            steps {

                git(
                    branch: 'main',
                    url: 'https://github.com/MONIV369/DevSecOps-CI-CD-Pipeline.git'
                )

            }

        }



        stage('SonarQube Analysis') {

            steps {

                withSonarQubeEnv('SonarQube') {

                    sh '''
                    ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectKey=linux-tweet-app \
                    -Dsonar.sources=.
                    '''

                }

            }

        }



//        stage('OWASP Dependency Check') {
//
//            steps {
//
//              sh '''

//                /opt/dependency-check/bin/dependency-check.sh \
//                --scan . \
//                --format HTML \
//                --out dependency-check-report

//                '''

//            }


            post {

                always {

                    publishHTML(
                        target: [
                            allowMissing: true,
                            alwaysLinkToLastBuild: true,
                            keepAll: true,
                            reportDir: 'dependency-check-report',
                            reportFiles: 'dependency-check-report.html',
                            reportName: 'OWASP Dependency Check Report'
                        ]
                    )

                }

            }

        }



        stage('Docker Build') {

            steps {

                sh '''

                docker build \
                -t ${IMAGE_NAME}:${IMAGE_TAG} .

                '''

            }

        }



        stage('Trivy Image Scan') {

            steps {

                sh '''

                trivy image \
                --severity HIGH,CRITICAL \
                --exit-code 1 \
                ${IMAGE_NAME}:${IMAGE_TAG}

                '''

            }

        }



        stage('Docker Push') {

            steps {


                withDockerRegistry(
                    credentialsId: 'dockerhub-creds',
                    url: 'https://index.docker.io/v1/'
                ) {


                    sh '''

                    docker push ${IMAGE_NAME}:${IMAGE_TAG}

                    '''

                }

            }

        }


    }



    post {


        success {

            echo "CI/CD Pipeline completed successfully"

        }


        failure {

            echo "Pipeline failed. Check console output"

        }


        always {

            echo "Pipeline execution finished"

        }


    }

}
