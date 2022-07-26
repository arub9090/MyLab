pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
    }

     environment{
       ArtifactId = readMavenPom().getArtifactId()
       Version = readMavenPom().getVersion()
       Name = readMavenPom().getName()
       GroupId = readMavenPom().getGroupId()
    }

    stages {
        // Specify various stage with in stages

        // stage 1. Build
        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }
        }

        // Stage2 : Testing
        stage ('Test'){
            steps {
                echo ' testing......'

            }
        }




// stage 3 publish the artifact on the nexus repo
    stage ("Publish to Nexus"){
        steps {
            script {

                def NexusRepo= Version.endsWith("SNAPSHOT") ? "ArifDevOpsLab-SNAPSHOT" : "ArifDevOpsLab-RELEASE"

                nexusArtifactUploader artifacts: [[artifactId: "${ArtifactId}",
             classifier: '', file: "target/${ArtifactId}-${Version}.war",
              type: 'war']], credentialsId: 'a4639f6b-04c7-4db8-b057-68c30a8931f1',
               groupId: "${GroupId}", nexusUrl: '172.20.10.125:8081', nexusVersion: 'nexus3',
                protocol: 'http', repository: "${NexusRepo}", version: "${Version}"
            }
        }
    }


// Stage 4 : Print some information
        stage ('Print Environment variables'){
                    steps {
                        echo "Artifact ID is '${ArtifactId}'"
                        echo "Version is '${Version}'"
                        echo "GroupID is '${GroupId}'"
                        echo "Name is '${Name}'"
                    }
                }



  // Stage 5 : Deploying the build artifact to Apache Tomcat
        stage ('Deploy to Tomcat'){
            steps {
                echo "Deploying ...."
                sshPublisher(publishers: 
                [sshPublisherDesc(
                    configName: 'Ansible_controller', 
                    transfers: [
                        sshTransfer(
                                cleanRemote:false,
                                execCommand: 'ansible-playbook /opt/playbooks/downloadanddeploy_as_tomcat_user.yaml -i /opt/playbooks/hosts',
                                execTimeout: 120000
                        )
                    ], 
                    usePromotionTimestamp: false, 
                    useWorkspaceInPromotion: false, 
                    verbose: false)
                    ])
            
            }
        }




  // Stage 6 : Deploying the build artifact to docker
        stage ('Deploy to Docker'){
            steps {
                echo "Deploying to docker...."
                sshPublisher(publishers: 
                [sshPublisherDesc(
                    configName: 'Ansible_controller', 
                    transfers: [
                        sshTransfer(
                                cleanRemote:false,
                                execCommand: 'ansible-playbook /opt/playbooks/downloadanddeploy_docker.yaml -e ansible_python_interpreter=/usr/bin/python3 -i /opt/playbooks/hosts',
                                execTimeout: 120000
                        )
                    ], 
                    usePromotionTimestamp: false, 
                    useWorkspaceInPromotion: false, 
                    verbose: true)
                    ])
            
            }
        }



         // Stage7 : Publish the source code to Sonarqube
        stage ('Sonarqube Analysis'){
            steps {
                echo ' Source code published to Sonarqube for SCA......'
                withSonarQubeEnv('sonarqube'){ // You can override the credential to be used
                     sh 'mvn sonar:sonar'
                }

            }
        }

        
    }

}