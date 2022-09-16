pipeline {

	agent any

	environment {
		AWS_ACCESS_KEY_ID     = credentials('Access_key_ID')
  		AWS_SECRET_ACCESS_KEY = credentials('Secret_access_key')
		AWS_S3_BUCKET = 'duihua-s3'
		 ARTIFACT_NAME = "DuiHua-0.0.1-SNAPSHOT.war"
		AWS_EB_APP_NAME = 'duihua'
        AWS_EB_ENVIRONMENT_NAME = 'Duihua-env'
        AWS_EB_APP_VERSION = "${BUILD_ID}"
	}

	stages {      
	stage('Build'){
            steps {
                sh "mvn clean"

                sh "mvn compile"
            }
        }

        
         stage('Test') {
            steps {
                
                sh "mvn test"

            }

            post {
                always {
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/TEST-*.xml'

                }
            }
        }
	stage('Quality Scan'){
            steps {
                sh '''
                mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=duihua \
                    -Dsonar.host.url=http://ec2-34-227-115-247.compute-1.amazonaws.com:9000 \
                    -Dsonar.login=sqp_08d170708e9ab4dab4ec81e2927f1d1e28d15673
                '''
            }
        }	
     
        stage('Deploy') {
            steps {
                sh 'aws configure set region us-east-1	'
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT_NAME --version-label $AWS_EB_APP_VERSION'
            }
	}
       
    }

}
