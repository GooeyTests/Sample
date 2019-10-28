pipeline {
    agent {
        label "default-java"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Going to check out the things !"
                git url: "https://github.com/Terasology/Sample.git", credentialsId: "GooeyHub"
            }
        }
        stage('Prep workspace') {
            steps {
                copyArtifacts(projectName: "Terasology/TerasologySonar", filter: "modules/Core/build.gradle", flatten: true, selector: lastSuccessful())
                copyArtifacts(projectName: "Terasology/TerasologySonar", filter: "*, gradle/wrapper/**, config/**, natives/**", selector: lastSuccessful())
                sh 'ls'
            }
        }
        stage('Build') {
            steps {
                
                rtGradleResolver (
                    id: 'teraResolver',
                    serverId: 'TerasologyArtifactory',
                    repo: 'virtual-repo-live'
                )
                  
                rtGradleDeployer (
                    id: 'teraDeployer',
                    serverId: 'TerasologyArtifactory',
                    repo: 'terasology-snapshot-local',
                )
                
                rtGradleRun (
                    // Set to true if the Artifactory Plugin is already defined in build script.
                    usesPlugin: true,
                    // Set to true if you'd like the build to use the Gradle Wrapper.
                    useWrapper: true,
                    tasks: 'clean check jar generatePomFileForMavenJavaPublication artifactoryPublish',
                    resolverId: 'teraResolver',
                    deployerId: 'teraDeployer',
                )
                
                
                rtPublishBuildInfo (
                    serverId: 'TerasologyArtifactory'
                )
            }
        }
    }
}
