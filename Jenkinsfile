pipeline {
    agent any

    stages {
        stage("Restore nuget") {
            steps {
                sh 'dotnet restore'
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

            // post {
            //     always {
            //         junit testResults:'**/pipelines-dotnet-core.dll'
            //         }
            // }
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