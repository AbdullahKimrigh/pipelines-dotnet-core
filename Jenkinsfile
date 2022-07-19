pipeline {
    agent any

    stages {
        stage("Restore Package") {
            steps {
                bat "dotnet Restore"
            }
        }
        
        stage('Clean') {
            steps {
                bat "dotnet clean"
            }
        }
        
        stage('Build') {
            steps {
                bat "dotnet build **/pipelines-dotnet-core.csproj --configuration release"
                }
            }
        
        stage('Test') {
            steps {
                bat "dotnet test --logger trx **/pipelines-dotnet-core.csproj"
                }
            post {
                always {
                    mstest testResultsFile:"**/*.trx", keepLongStdio: true
                }
            }
            }
        
        stage('Publish') {
            steps{
            bat "dotnet publish **/pipelines-dotnet-core.csproj"
            }
        
            post{
                success {
                    archiveArtifacts artifacts: '**/bin/Debug/net6.0/.dll', followSymlinks: false
                }
            }
        }
    }