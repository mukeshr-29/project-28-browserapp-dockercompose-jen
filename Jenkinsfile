pipeline{
    agent any
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
    }


    stages{
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('git checkout'){
            steps{
                git 'https://github.com/mukeshr-29/project-28-browserapp-dockercompose-jen.git'
            }
        }
        stage('static code analysis'){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-token', installationName: 'sonar-server'){
                        sh'''
                        $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=virtualBrowser \
                        -Dsonar.projectKey=virtualBrowser
                        '''
                    }
                }
            }
        }
        stage('quality check'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('dependency check'){
            steps{
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'dp-check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        // stage('docker build and push'){
        //     steps{
        //         script{
        //             withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker'){
                        
        //             }
        //         }

        //     }
        // }
    }
}