pipeline{
 
 
    agent none
 
    stages{
        stage('Build') {
            agent{
                docker{
                    image 'nexus.spawarrl.com:18443/maven:3.5.4-jdk-8-alpine'
                    registryUrl 'https://nexus.spawarrl.com:18443'
                    registryCredentialsId 'nexus3admin'
                    args '-v ${HOME}/.m2:/.m2:z -e _JAVA_OPTIONS="-Duser.home=/"'
                }
            }          
            steps{
 
                sh 'mvn clean test -DskipTests -U'
 
            }
        }
        stage('Test') {
            agent{
                docker{
                    image 'nexus.spawarrl.com:18443/rgielen/selenium-firefox-maven'
                    registryUrl 'https://nexus.spawarrl.com:18443'
                    registryCredentialsId 'nexus3admin'
                    args '-v ${HOME}/.m2:/.m2:z -v ${HOME}/.sonar:/.sonar:z -e _JAVA_OPTIONS="-Duser.home=/"'
                }
            }
            steps{
                sh 'Xvfb :99 -ac &'
                sh 'mvn test'
            }
        }
        stage('Archive Results'){
            agent any
            steps{

                archiveArtifacts 'test-output/**'
            }
        }
        // stage('SonarQube'){
        //     agent any
        //     steps{
        //         parallel(
        //             SonarQube: {
        //                 withSonarQubeEnv('SonarQube') {
 
        //                     sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar"
        //                 }
        //             },
        //             JUnit: {
        //                 junit '**/target/surefire-reports/TEST-*.xml'
        //                 archive 'target/*.jar'
        //             }
        //         )
        //     }
        // }
    }     
    post{
        always{
            mail to: 'mshajibu@spawarrl.com',
                 subject: "${currentBuild.currentResult} - ${currentBuild.fullDisplayName}",
                 body: "${env.BUILD_URL}"
        }
    }
}
