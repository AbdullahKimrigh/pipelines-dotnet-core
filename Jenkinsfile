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
        
        SONAR_PROJECT_NAME = "dotnet-app"
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

            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    }
            }
        }

        stage('Quality Scan'){
            steps {
                sh '''
                dotnet tool install --global dotnet-sonarscanner
                dotnet sonarscanner begin /k:$SONAR_PROJECT_NAME /
                d:sonar.host.url=http://$SONAR_IP  /
                d:sonar.login=$SONAR_TOKEN                
                dotnet build
                dotnet sonarscanner end /d:sonar.login=$SONAR_TOKEN
                '''
            }
        }

        stage('Package') {
            steps {                
                sh "dotnet List Package"
            }

            post {
                success {
                    archiveArtifacts artifacts: '**/target/**.war', followSymlinks: false                  
                }
            }
        }
        
        stage('Publish') {
            steps{
                sh 'dotnet publish ./pipelines-dotnet-core.csproj'
            }
        
            post{
                success {
                    archiveArtifacts artifacts: '**/bin/Debug/net6.0/*.dll', followSymlinks: false
                }
            }
        }
    }
}