pipeline {
    agent any

    environment {

        // AWS configuration by jenkins
        AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-secret-key-id') //AWS access key id for user
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key') //AWS secret key for user

        AWS_S3_BUCKET = "dotnet-artifacts-bucket" //Bucket Name
        ARTIFACT_NAME = "hello-world.war" //Name of the Artifacts
        AWS_EB_APP_NAME = "dotnet-webapp" //Name of the AWS elasticbeans application
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_EB_ENVIRONMENT = "Dotnetwebapp-env" //Name os the AWS elasticbeans environemnt of the application
        
        SONAR_PROJECT_NAME = "**/dotnet-app"
        SONAR_IP = "54.226.50.200"
        SONAR_TOKEN = "sqp_48241ccbd8e60a60e5e343d611cd07e5bb90bd10"
    }

    stages {
        stage("Restore") {
            steps {
                sh 'dotnet restore'
                sh 'dotnet clean'
            }
        }
        
        stage('Build') {
            steps {
                sh 'dotnet build --configuration release ./pipelines-dotnet-core.csproj'
            }
        }
        
        stage('Test') {
            steps {
                sh 'dotnet test ./pipelines-dotnet-core.csproj'
            }
        }

        stage('Quality Scan'){
            steps {
                sh '''
                dotnet ef migrations add InitialCreate
                dotnet sonarscanner begin /
                    k:$SONAR_PROJECT_NAME /
                    d:sonar.host.url="http://$SONAR_IP" /
                    d:sonar.login=$SONAR_TOKEN
                dotnet build
                dotnet sonarscanner end /d:sonar.login=$SONAR_TOKEN
                '''
            }
        }

        stage('Publish') {
            steps {                
                sh "dotnet List Package"
                sh 'dotnet publish'
            }

            post {
                success {
                    archiveArtifacts artifacts: '**/target/**.war', followSymlinks: false                  
                }
            }
        }
        
        stage('Publish Artifacts') {
            steps{
                sh "aws configure set region us-east-1"
                sh "aws s3 cp ./target/**.war s3://$AWS_S3_BUCKET/$ARTIFACT_NAME"
            }
        }

        stage('Deploy') {
            steps {
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'                
            }
        } 
    }
}