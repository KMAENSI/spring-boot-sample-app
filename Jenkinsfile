pipeline {
    // run on jenkins nodes tha has java 8 label
    agent any
    // global env variables
    environment {
        EMAIL_RECIPIENTS = 'kamel2009@gmail.com'
    }
    stages {

        stage('Build with unit testing') {
            steps {
                // Run the maven build
                script {
                    // Get the Maven tool.
                    // ** NOTE: This 'M3' Maven tool must be configured
                    // **       in the global configuration.
                    echo 'Pulling...' + env.GIT_BRANCH
                    
                    if (isUnix()) {
                                               sh "mvn -Dintegration-tests.skip=true -Dbuild.number=${targetVersion} clean package"
                                             
                    } else {
                        bat(/"${mvnHome}\bin\mvn" -Dintegration-tests.skip=true clean package/)
                        def pom = readMavenPom file: 'pom.xml'
                        print pom.version
                        junit '**//*target/surefire-reports/TEST-*.xml'
                        archive 'target*//*.jar'
                    }
                }

            }
        }
    }
   
}
