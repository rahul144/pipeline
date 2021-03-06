pipeline{
    agent any
    environment{
        scannerHome = tool name: 'sonar_scanner_dotnet', type: 'hudson.plugins.sonar.MsBuildSQRunnerInstallation'
    }
    stages{
        
        stage('nuget restore') {
            steps {
             bat "dotnet restore WebApplication4\\WebApplication4.csproj"
            }
        }
        
        stage('Start sonarqube analysis'){
           steps{
               withSonarQubeEnv('SonarQubeScanner'){
                   bat "dotnet ${scannerHome}/SonarScanner.MSBuild.dll begin /k:rahul-valecha"
               }
            }
        }
        stage('Code build'){
           steps{
              bat "dotnet build WebApplication4.sln --configuration Release"
            }
        }
        stage('Stop sonarqube analysis'){
           steps{
               withSonarQubeEnv('SonarQubeScanner'){
                    bat "dotnet ${scannerHome}/SonarScanner.MSBuild.dll end"
               }
            }
        }
        
        stage('Docker image'){
             steps{
               bat "docker build -t i-rahulvalecha-master WebApplication4"
             }
        }
         stage('Container'){
            parallel {
                stage('PreContainerCheck'){
                     steps{
                       bat 'docker ps -aq --filter "name=c_rahulvalecha_master" | findstr "." && docker stop c_rahulvalecha_master && docker rm -fv c_rahulvalecha_master'
                     }
                }
                
            }
        }
        stage('Docker Deployment'){
            steps{
               bat "docker run -d -p 6002:80 --name c_rahulvalecha_master i-rahulvalecha-master"
             }
        }
        stage('Helm Chart Deployment'){
            steps{
               bat "helm install master-branch master-chart --set appname=master-app --set  nodePort=30157 --set image=dtr.nagarro.com:443/i-rahulvalecha-master"
             }
        }

 

        
    }
}
