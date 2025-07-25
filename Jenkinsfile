node {
    def SONARQUBE_HOSTNAME = 'sonarqube'

    def GRADLE_HOME = tool name: 'gradle-4.10.2', type: 'hudson.plugins.gradle.GradleInstallation'
    sh "${GRADLE_HOME}/bin/gradle tasks"

    stage('prep') {
        git url: 'https://github.com/nrejepov/devops-webapp.git'                
    }

    stage('sonar-scanner') {
    tools {
        // This line forces this stage to use Java 11
        jdk 'jdk11' 
    }
    steps {
        def sonarqubeScannerHome = tool name: 'sonar', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
        withCredentials([string(credentialsId: 'sonar', variable: 'sonarLogin')]) {
            // Using triple-quotes makes the command easier to read
            sh """
               ${sonarqubeScannerHome}/bin/sonar-scanner \
               -e \
               -Dsonar.host.url=http://${SONARQUBE_HOSTNAME}:9000 \
               -Dsonar.login=${sonarLogin} \
               -Dsonar.projectName=WebApp \
               -Dsonar.projectVersion=${env.BUILD_NUMBER} \
               -Dsonar.projectKey=GS \
               -Dsonar.sources=src/main/ \
               -Dsonar.tests=src/test/ \
               -Dsonar.java.binaries=build/**/* \
               -Dsonar.language=java
            """
        }
    }
}
