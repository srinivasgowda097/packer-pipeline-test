#!groovy

node {
    git url: 'https://github.com/jfrogtraining/docker-lifecycle-scripts.git'

    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server('newartifact')
    
    def workspace = pwd()
    
    //def user = 'raghavb'
    //def password = 'AKCp2V5euMXujN5ecnEs32vLDMsNsqpRbbHShye6qGuAQ2NNZBGsXAJkvDa6Mx9wtQEWbALHm'
    
    def BUILD_NUMBER = currentBuild.number

    // Read the upload spec which was downloaded from github.
    def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "$workspace/docker-app/*.*",
                              "target": "docker-local2/"
                            }
                         ]
                        }"""
    
    // Upload to Artifactory.
    def buildInfo1 = server.upload spec: uploadSpec

    // Read the upload spec and upload files to Artifactory.
    def downloadSpec = """{
                         "files": [
                          {
                              "pattern": "gradle-dev-local/**/webservice-1.*.war",
                              "target": "$workspace/docker-app/war/"
                            }
                         ]
                        }"""
                        
    def buildInfo2 = server.download spec: downloadSpec
    
    server.download(downloadSpec)
    
    dir ('docker-app'){
        
    //Move the latest gradle war filename only - remove previous copy. 
    //sh 'if [ -f war/*.war] ; then'
    sh 'rm war/*.war'
    //sh 'fi'
    sh "find war -name '*.war' > warpathtemp"
    def warPath = readFile('warpathtemp').trim()
    print "DEBUG: parameter warPath = ${warPath}"
    
    int index = warPath.lastIndexOf("/");
    def warFile = warPath.substring(index + 1);
    print "DEBUG: parameter warFile = ${warFile}"
    
    print "DEBUG: parameter buildnumber = ${BUILD_NUMBER}"
    
    sh "mv $warPath war/${warFile}"   
        
    stage 'Docker build'
    docker.build('gcartifactory-us.jfrog.info:5000/docker-app:100')
    
    stage 'Docker push'
    docker.withRegistry('https://gcartifactory-us.jfrog.info:5000', '$user:$password') {
    docker.image('docker-app').push('${BUILD_NUMBER}')
    //sh "docker push gcartifactory-us.jfrog.info:5000/docker-app:100"
    }
    
    sh 'rm -rf war/*.war'
    //Retag image with latest
    sh 'sed -E "s/@/$BUILD_NUMBER/" retag-gcartifactory.json > retag-gcartifactory_out.json'
    sh 'cat retag-gcartifactory_out.json'
    sh "curl --user '${user}:${password}' -X POST https://gcartifactory-us.jfrog.info/artifactory/api/plugins/execute/dockerRetag -T retag-gcartifactory_out.json"
    
    }

    // Merge the upload and download build-info objects.
    buildInfo1.append buildInfo2

    // Publish the build to Artifactory
    server.publishBuildInfo buildInfo1
}
