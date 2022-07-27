pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
    }

    environment{
        ArtifactId=readMavenPom().getArtifactId()
        Version=readMavenPom().getVersion()
        Name= readMavenPom().getName()
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
            nexusArtifactUploader artifacts: [[artifactId: 'VinayDevOpsLab', classifier: '', file: 'target/VinayDevOpsLab-0.0.5-SNAPSHOT.war', type: 'war']], credentialsId: 'a4639f6b-04c7-4db8-b057-68c30a8931f1', groupId: 'com.vinaysdevopslab', nexusUrl: '172.20.10.125:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'ArifDevOpsLab-SNAPSHOT', version: '0.0.5-SNAPSHOT'
        }
    }


    // state 4 for utilizing the readMavenPom
    stage ("Print the env Variables Now"){
        steps {
            echo "The Artifact ID is- '${ArtifactId}'"
            echo "Version is = '${Version}'"
            echo "Name will be : '${Name}"
        }
    }



     stage ('Deploy'){
        steps {
            echo 'Deploying.....'
        }
     }

        
        
    }

}