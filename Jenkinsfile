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
}
}
