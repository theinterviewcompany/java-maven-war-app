pipeline{
    agent{
        label 'agent'
    }

    tools {
        maven 'Maven_3.9.6'
    }

    stages{
        stage('SCM Checkout'){
            steps{
                checkout changelog: false, poll: false, scm: scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/theinterviewcompany/java-maven-war-app.git']])
            }

        }

        stage('Maven-Build'){
            steps{
                sh 'mvn clean install'
            }
        }

        stage('Sonar Scan'){
            steps{
                withSonarQubeEnv("SonarQube") {
                    sh "${tool("Sonar_5.0.1")}/bin/sonar-scanner \
                    -Dsonar.host.url=http://43.204.212.36:9000/ \
                    -Dsonar.login=sqp_beebfd0692ea71e1aaf05aa3754caaf426e47762 \
                    -Dsonar.java.binaries=target \
                    -Dsonar.projectKey=java-maven-war-app"                    
                }
            }
        }

        stage('Nexus Upload'){
            steps{
                sh 'mvn -s settings.xml clean deploy'
            }
        }

        stage('deployment'){
            steps{
                sh 'ansible-playbook -i inventory deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'
            }
        }

    }
}
