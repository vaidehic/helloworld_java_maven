pipeline{
    agent any
    tools{
        maven "Maven 3.6.3"
        jdk "JDK-11"
    }       
    stages {       
        stage('Initialize'){
            steps{
                echo "PATH = ${M2_HOME}/bin:${PATH}"
                echo "M2_HOME = /opt/maven"
            }
        }
        stage('Build'){
            steps{
                echo 'building'
                post {
                 always {
                     jiraSendBuildInfo site: 'example.atlassian.net'
                 }
             }
            }
            
            
        }
        stage('Deploy - Staging') {
             when {
                 branch 'master'
             }
             steps {
                 echo 'Deploying to Staging from master...'
             }
             post {
                 always {
                     jiraSendDeploymentInfo environmentId: 'us-stg-1', environmentName: 'us-stg-1', environmentType: 'staging'
                 }
             }
         }
         stage('Deploy - Production') {
            when {
                branch 'master'
            }
            steps {
                echo 'Deploying to Production from master...'
            }
            post {
                always {
                    jiraSendDeploymentInfo environmentId: 'us-prod-1', environmentName: 'us-prod-1', environmentType: 'production'
                }
            }
         }
        stage('Compile'){
            steps{
                echo "COMPILE"
             bat 'mvn clean install'
            }
        }
        stage('Sonar Analysis') {
            steps {
                // use the SonarQube Scanner to analyze the project
                withSonarQubeEnv('SonarQubeServer') {
                    bat 'mvn sonar:sonar'
                }
            }
        }
        stage("Quality gate") {
            steps {
                waitForQualityGate abortPipeline: true
            }
    }   
        stage('Upload Artifact') {
              steps {
                  script{
                      def server = Artifactory.server 'JfrogServer'
                      def uploadSpec = """{ 
                          "files":[
                              {
                                  "pattern":"target./*.jar",
                                  "target": "Simple_java_project_Repo/"
                              }
                              ]
                              }"""
                              server.upload(uploadSpec)
                              }
                              }
                              }
                          
}
}
