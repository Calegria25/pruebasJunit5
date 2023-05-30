pipeline {
    agent any 

    tools { 
        maven 'mavenjenkins'
        jdk 'jenkisjava'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/MiguelAngelRamos/pruebasJunit5.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'mvn test'
                }
            }
        }

    stage('Sonar Scanner') {
        steps {
            script {
                def sonarqubeScannerHome = tool name: 'sonar', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                withCredentials([string(credentialsId: 'sonar', variable: 'sonarLogin')]) {
                    sh "${sonarqubeScannerHome}/bin/sonar-scanner -e -Dsonar.host.url=http://SonarQube:9001 -Dsonar.login=${sonarLogin} -Dsonar.projectName=gs-gradle -Dsonar.projectVersion=${env.BUILD_NUMBER} -Dsonar.projectKey=GS -Dsonar.sources=src/main/java/cl/awakelab/junitapp -Dsonar.tests=src/test/java/cl/awakelab/junitapp -Dsonar.language=java -Dsonar.java.binaries=."
                }
            }
        }
    }

    //
    stage('Nexus Upload') {
        steps {
            nexusArtifactUploader(
                nexusVersion: 'nexus3',
                protocol: 'http',
                nexusUrl: 'nexus:8081',
                groupId: 'cl.awakelab.junitapp',
                version: '0.0.1-SNAPSHOT',
                repository: 'maven-snapshots',
                credentialsId: 'nex',
                artifacts: [
                    [artifactId: 'proyectoJunit',
                    classifier: '',
                    file: 'target/proyectoJunit-0.0.1.jar',
                    type: 'jar'],
                    [artifactId: 'proyectoJunit',
                    classifier: '',
                    file: 'pom.xml',
                    type: 'pom']
                ]
            )
        }
    }

    //
    }
}
