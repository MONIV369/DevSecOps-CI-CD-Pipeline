pipeline {

    agent any


    environment {

        IMAGE_NAME = "moniv369/linux-tweet-app"

        IMAGE_TAG = "${BUILD_NUMBER}"

    }


    stages {


        stage('Checkout Code') {

            steps {

                git branch: 'master',
                url: 'https://github.com/dockersamples/linux_tweet_app.git'

            }

        }



        stage('SonarQube Analysis') {

            steps {

                withSonarQubeEnv('SonarQube') {

                    sh '''
                    sonar-scanner \
                    -Dsonar.projectKey=linux-tweet-app \
                    -Dsonar.sources=.
                    '''

                }

            }

        }



        stage('OWASP Dependency Check') {

            steps {

                sh '''
                dependency-check.sh \
                --scan . \
                --format HTML \
                --out dependency-check-report
                '''

            }

        }



        stage('Docker Build') {

            steps {

                sh """

                docker build \
                -t ${IMAGE_NAME}:${IMAGE_TAG} .

                """

            }

        }



        stage('Trivy Image Scan') {

            steps {

                sh """

                trivy image \
                --severity HIGH,CRITICAL \
                --exit-code 1 \
                ${IMAGE_NAME}:${IMAGE_TAG}

                """

            }

        }



        stage('Docker Push') {

            steps {

                withDockerRegistry(credentialsId: 'dockerhub-creds') {

                    sh """

                    docker push ${IMAGE_NAME}:${IMAGE_TAG}

                    """

                }

            }

        }


    }


    post {


        always {

            echo "Pipeline completed"

        }


    }

}
