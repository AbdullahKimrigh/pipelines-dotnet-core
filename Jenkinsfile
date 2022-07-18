pipeline {
    agent any

    stages {
        stage('Restore') {
            steps {
                bat "dotnet restore **/pipelines-dotnet-core.csproj"
            }
        }
        
        stage('Clean') {
            steps {
                bat "dotnet clean **/pipelines-dotnet-core.csproj"
            }
        }
        
        stage('Build') {
            steps {
                bat "dotnet build **/pipelines-dotnet-core.csproj"
                }
            }
        
        stage('Test') {
            steps {
                bat "dotnet test **/pipelines-dotnet-core.csproj"
                }
            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
            }
        
        stage('Publish') {
            steps{
            bat "dotnet publish **/pipelines-dotnet-core.csproj"
            }
        
            post{
                success {
                    archiveArtifacts artifacts: '*/bin/Debug/net6.0/.dll', followSymlinks: false
                }
            }
        }
        }