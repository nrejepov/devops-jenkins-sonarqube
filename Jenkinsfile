node {
    def SONARQUBE_HOSTNAME = 'sonarqube'

    def GRADLE_HOME = tool name: 'gradle-4.10.2', type: 'hudson.plugins.gradle.GradleInstallation'
    sh "${GRADLE_HOME}/bin/gradle tasks"

    stage('prep') {
        git url: 'https://github.com/nrejepov/devops-webapp.git'
    }

    stage('sonar-scanner') {
    // 1. Define the tools you need
    def jdk = tool name: 'jdk11'
    def scannerHome = tool name: 'sonar'

    // 2. Wrap your commands in withEnv to add the JDK to the PATH
    withEnv(["JAVA_HOME=${jdk}", "PATH+MAVEN=${jdk}/bin"]) {
        withCredentials([string(credentialsId: 'sonar', variable: 'sonarLogin')]) {
            sh """
               ${scannerHome}/bin/sonar-scanner \
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
}
