pipeline{
    agent any
    tools{
        jdk "JDK8"
        maven "MAVEN3"
    }
    environment {
        registry = "agalal9111/vproargocd"
        registryCredential = 'dockerhub'
    }
    stages{
       /* stage('clone the source code'){
            steps {
                 git branch: "vp-rem" , url:"https://github.com/devopshydclub/vprofile-project.git"
            }
        }*/
        stage("build"){
            steps{
                sh 'mvn install '
            }
            post{
                success{
                    echo'success build!'
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
    }
    stage('test'){
        steps{
            sh 'mvn test'
        }
        
    }
    stage('check style'){
        steps{
            sh 'mvn checkstyle:checkstyle'
        }
    }

            stage('Sonar Analaysis'){
                    environment{
                                        scannerHome = tool 'Sonar4.8'
                                    }
            steps{
              withSonarQubeEnv('Sonar') {
                   sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
              }
            }
        }

        stage('Build App Image') {
          steps {
            script {
              dockerImage = docker.build registry + ":V$BUILD_NUMBER"
            }
          }
        }

        stage('Upload Image'){
          steps{
            script {
              docker.withRegistry('', registryCredential) {
                dockerImage.push("V$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
          }
        }
        stage('Remove Unused docker image') {
          steps{
            sh "docker rmi $registry:V$BUILD_NUMBER"
          }
        }

 }
}
