pipeline {
    agent any

    stages {
        stage("Restore nuget") {
            steps {
                bat 'dotnet restore'
                bat 'nuget restore BigProject.sln'
            }
        }
        
        stage('Build') {
            steps {
                bat 'dotnet build --configuration release **/pipelines-dotnet-core.csproj'
            }
        }
        
        stage('Test') {
            steps {
                bat 'dotnet test --logger trx **/pipelines-dotnet-core.csproj'
            }

            post {
                always {
                    mstest testResultsFile:"**/*.trx", keepLongStdio: true
                    }
            }
        }
        
        stage('Publish') {
            steps{
            bat 'dotnet publish **/pipelines-dotnet-core.csproj'
            }
        
            post{
                success {
                    archiveArtifacts artifacts: '**/bin/Debug/net6.0/.dll', followSymlinks: false
                }
            }
        }
    }
}