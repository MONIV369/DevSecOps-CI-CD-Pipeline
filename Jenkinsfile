pipeline {

    agent any


    environment {

        IMAGE_NAME = "moniv369/linux-tweet-app"

        IMAGE_TAG = "${BUILD_NUMBER}"

    }


    stages {


        stage('Checkout Code') {

            steps {

                git branch: 'main',
                url: 'https://github.com/MONIV369/DevSecOps-CI-CD-Pipeline.git'

            }

        }



        stage('SonarQube Analysis') {

            steps {

                    sh '''
			sonar-scanner \
			  -Dsonar.projectKey=linux-tweet-app \
			  -Dsonar.sources=. \
			  -Dsonar.host.url=http://52.14.134.64:9000 \
			  -Dsonar.login=sqp_a3f6d9c5a4f98b9f347556740ce2abe7b9687b6c                

			'''

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
	
        		withDockerRegistry(
            		credentialsId: 'dockerhub-creds',
            		url: 'https://index.docker.io/v1/'
        		) {

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
