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
                                        
                       def targetVersion = getDevVersion()
                        sh "mvn -Dintegration-tests.skip=true -Dbuild.number=${targetVersion} clean package "
                                             
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
   
    stage('Integration tests') {
               // Run integration test
               steps {
                   script {
                       def mvnHome = tool 'Maven 3.5.2'
                       if (isUnix()) {
                           // just to trigger the integration test without unit testing
                           sh "mvn  verify -Dunit-tests.skip=true"
                       } else {
                           bat(/"${mvnHome}\bin\mvn" verify -Dunit-tests.skip=true/)
                       }

                   }
                   // cucumber reports collection
                   cucumber buildStatus: null, fileIncludePattern: '**/cucumber.json', jsonReportDirectory: 'target', sortingMethod: 'ALPHABETICAL'
               }
           }
}
 def getDevVersion() {
    def gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
    def versionNumber;
    if (gitCommit == null) {
        versionNumber = env.BUILD_NUMBER;
    } else {
        versionNumber = gitCommit.take(8);
    }
    print 'build  versions...'
    print versionNumber
    return versionNumber
}
