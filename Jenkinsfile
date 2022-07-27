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
            nexusArtifactUploader artifacts: [[artifactId: "${ArtifactId}",
             classifier: '', file: 'target/VinayDevOpsLab-0.0.5-SNAPSHOT.war',
              type: 'war']], credentialsId: 'a4639f6b-04c7-4db8-b057-68c30a8931f1',
               groupId: "${GroupId}", nexusUrl: '172.20.10.125:8081', nexusVersion: 'nexus3',
                protocol: 'http', repository: 'ArifDevOpsLab-SNAPSHOT', version: "${Version}"
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



     stage ('Deploy'){
        steps {
            echo 'Deploying.....'
        }
     }

        
        
    }

}