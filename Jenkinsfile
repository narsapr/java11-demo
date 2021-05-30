pipeline {
    agent any
    environment {
        SONAR_TOKEN= 'c441ff5dec3d47ba86da3e2af4a9466d6695c19c'
        APP_HOME='/home/app'
        PRAGRA_BATCH='devs'
    }
    options { 
        quietPeriod(15) 
    }
    parameters { 
            choice(name: 'ENV_TO_DEPLOY', 
            choices: ['ST', 'UAT', 'STAGING'], description: 'Select a Env to deploy') 

             booleanParam(name: 'RUN', defaultValue: true, description: 'SELECT TO RUN')
    }
    triggers {
        pollSCM('* * * * *')
    }
    tools{
        maven  'm3'
        jdk 'jdk11'
    }

    stages {
        stage('Clean Ws') {
            steps{
                cleanWs()
            }
        }
        stage('Git CheckoutOut'){
            steps{
                checkout scm 
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
       stage('Static Code Analaysis') {
            steps {
                withSonarQubeEnv(credentialsId: 'sonartoken', installationName: 'sonarcloud') {
                    sh 'mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar'
                }
            }
        }
         stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

         stage('Package') {
            steps {
                sh 'mvn package'
            }
        }
        
      stage('Publish to Artifactory') {
         steps{
             rtUpload (
                 serverId: 'Artifactory1',
                 spec: '''{
                     "files": [
                     {
                         "pattern": "*dummy*.jar",
                         "target": "Arifactory_local/dummy"
                         
                     },
                     
                     {
                         "pattern": "pox:xml",
                          "target": "Arifactory_local/dummy"
                     }
                
                     
                     ]
                         
                     
                 }''',
                 )
         }   
        }  
    }

    post {
        always {
            echo 'ALL GOOD '
        }
    }


}
