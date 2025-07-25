//START-OF-SCRIPT
node {
    def SONARQUBE_HOSTNAME = 'sonarqube'

    // This stage checks out the code first.
    stage('prep') {
        git url: 'https://github.com/nrejepov/devops-webapp.git'
    }

    // All subsequent commands must run inside the repository directory.
    dir('devops-webapp') {
        stage('build') {
            def GRADLE_HOME = tool name: 'gradle-4.10.2', type: 'hudson.plugins.gradle.GradleInstallation'
            sh "${GRADLE_HOME}/bin/gradle build"
        }

        stage('sonar-scanner') {
            // Get the path to the JDK 8 tool
            def jdk8 = tool name: 'jdk8', type: 'hudson.model.JDK'
            def sonarqubeScannerHome = tool name: 'sonar', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
            
            // Use withEnv to set the JAVA_HOME and PATH for this stage
            withEnv(["JAVA_HOME=${jdk8}", "PATH+JDK=${jdk8}/bin"]) {
                withCredentials([string(credentialsId: 'sonar', variable: 'sonarLogin')]) {
                    // CHANGES ARE HERE:
                    sh """
                       ${sonarqubeScannerHome}/bin/sonar-scanner \\
                       -Dsonar.host.url=http://${SONARQUBE_HOSTNAME}:9000 \\
                       -Dsonar.login=\\${sonarLogin} \\
                       -Dsonar.projectName=WebApp \\
                       -Dsonar.projectVersion=${env.BUILD_NUMBER} \\
                       -Dsonar.projectKey=GS \\
                       -Dsonar.sources=src/main/ \\
                       -Dsonar.java.binaries=build/**/* \\
                       -Dsonar.language=java
                    """
                }
            }
        }
    }
}
