pipeline{
    agent any
    stages{
        stage('Checkout') {
            steps {
             git changelog: false, credentialsId: 'rahulgit', poll: false, url: 'https://git.nagarro.com/NAGP/rahulvalecha'
            }
        }
        stage('nuget restore') {
            steps {
             bat "dotnet restore WebApplication4\\WebApplication4.csproj"
            }
        }
        stage('Code build'){
           steps{
              bat "dotnet build WebApplication4\\WebApplication4.csproj --configuration Release"
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
                        bat "docker build -t i-rahulvalecha-master WebApplication4"
                     }
                }
                stage('PushToDTR'){
                     steps{
                        bat "docker tag i-rahulvalecha-master dtr.nagarro.com:443/i-rahulvalecha-master"
                        bat "docker push dtr.nagarro.com:443/i-rahulvalecha-master"
                     }
                }
            }
        }
        stage('Docker Deployment'){
            steps{
               bat "docker run -d -p 6002:80 --name c_rahulvalecha_master i-rahulvalecha-master"
             }
        }

        
    }
}