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
        stage('docker build and tag'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker'){
                        // dir('/home/ubuntu/.jenkins/workspace/browser/.docker/brave')
                            sh 'docker build -t mukeshr29/bravebrowser /.docker/brave/.'
                        
                    }
                }
            }
        }
        stage('trivy img scan'){
            steps{
                sh 'trivy image mukeshr29/bravebrowser > trivyimg.txt'
            }
        }
        stage('docker img push to dockerhub'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker'){
                        sh 'docker push mukeshr29/bravebrowser'
                    }
                }
            }
        }
    }
}