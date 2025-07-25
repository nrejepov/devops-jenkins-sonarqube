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
            // Use a JDK 8 tool for this stage to ensure compatibility with SonarQube 7.1
            tools {
                jdk 'jdk8'
            }
            def sonarqubeScannerHome = tool name: 'sonar', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
            withCredentials([string(credentialsId: 'sonar', variable: 'sonarLogin')]) {
                sh "${sonarqubeScannerHome}/bin/sonar-scanner -e -Dsonar.host.url=http://${SONARQUBE_HOSTNAME}:9000 -Dsonar.login=${sonarLogin} -Dsonar.projectName=WebApp -Dsonar.projectVersion=${env.BUILD_NUMBER} -Dsonar.projectKey=GS -Dsonar.sources=src/main/ -Dsonar.tests=src/test/ -Dsonar.java.binaries=build/**/* -Dsonar.language=java"
            }
        }
    }
}
