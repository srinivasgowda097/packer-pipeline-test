node {
   // Mark the code checkout 'stage'....
   stage 'Checkout'

   // Get some code from a GitHub repository
   git url: 'https://github.com/jfrogtraining/docker-lifecycle-scripts'

   stage 'resolve'
   sh 'echo cleaning up'
   sh 'rm -rf assets/war'
   def artServer = Artifactory.server('newartifact')
   sh 'echo starting'
   def buildInfo = Artifactory.newBuildInfo()
buildInfo.env.capture = true
withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'artifactoryid',
usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
def uname=env.USERNAME
def pw=env.PASSWORD
artServer.username=uname
artServer.password=pw
 sh 'echo credentials applied'
 def curlstr="curl -u"+uname+':'+pw+" 'http://devapi.cloudmunch.com:8082/artifactory/"
/* def tomcatverstr=curlstr+ "api/search/latestVersion?g=org.apache&a=apache-tomcat&repos=tomcat-local'"
println(tomcatverstr)
 sh tomcatverstr+' > docker-framework/tomcat/version.txt'

env.TOMCAT_VERSION=readFile('docker-framework/tomcat/version.txt')
 sh 'echo $TOMCAT_VERSION'
 //sh 'rm -rf docker-framework/tomcat'
 //sh 'rm -rf docker-framework/jdk'
 sh 'ls -al docker-framework'
 def downloadSpec = """{
 "files": [
  {
   "pattern": "tomcat-local/java/jdk-8u91-linux-x64.tar.gz",
   "target": "docker-framework/jdk/jdk-8-linux-x64.tar.gz",
   "flat":"true"
  },
  {
   "pattern": "tomcat-local/org/apache/apache-tomcat/apache-tomcat-"""+env.TOMCAT_VERSION+""".tar.gz",
   "target": "docker-framework/tomcat/apache-tomcat-8.tar.gz",
   "flat":"true"
  }
  ]
}"""
println(downloadSpec)
//,
//  {
//   "pattern": "tomcat-local/org/apache/apache-tomcat/apache-tomcat-[RELEASE].tar.gz",
//   "target": "docker-framework/tomcat/apache-tomcat-8.tar.gz",
//   "flat":"true"
//  }  
artServer.download(downloadSpec, buildInfo)
myval=sh 'pwd'
println(myval)
sh 'ls -al'
sh 'ls -al docker-framework/jdk/'
sh 'ls -al docker-framework/tomcat/'
sh 'echo download complete'
   // Mark the code build 'stage'....
 //  stage 'Build'
   // Run the maven build
 //  sh "${mvnHome}/bin/mvn -Dmaven.test.failure.ignore clean package"
  */
   // step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])

    stage 'docker build'
    sh 'echo collecting'
    buildInfo.env.collect()
    sh 'echo starting build $BUILD_NUMBER'
            def tagName='hub.docker.com/r/srinivasgowda097/d_doccloud-main-2:'+env.BUILD_NUMBER
    dir('docker-framework') {
        docker.build(tagName)
    }
    //sh 'cd docker-framework; docker build -t jfrogtraining-docker-dev.jfrog.io/docker-framework:$BUILD_NUMBER .'
    stage 'docker push'
    def artDocker= Artifactory.docker(uname, pw)
    artDocker.push(tagName, 'docker-dev-local', buildInfo)
    echo 'pushed docker image'
    artServer.publishBuildInfo(buildInfo)
    //sh 'docker push jfrogtraining-docker-dev.jfrog.io/docker-hello:$BUILD_NUMBER'
 /*   stage 'testing'
    dir('docker-framework/framework-test')
    {
        def warverstr=curlstr+ "api/search/latestVersion?g=org.jfrog.example.gradle&a=webservice&repos=libs-release-local'"
       sh warverstr +' > war/version.txt'
       env.WARVER=readFile('war/version.txt')
        def downloadSpecWar = """{
 "files": [
  {
   "pattern": "libs-release-local/org/jfrog/example/gradle/webservice/"""+env.WARVER+"""/*.war",
   "target": "war/webservice.war",
   "flat":"true"
  }
  ]
}"""
    println(downloadSpecWar)
    artServer.download(downloadSpecWar)
        def tagNameTest='d_devops123-main-1:'+env.BUILD_NUMBER
        docker.build(tagNameTest)
withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'gcart-markg',
usernameVariable: 'GCUSERNAME', passwordVariable: 'GCPASSWORD']]) { 
    gcuname=env.GCUSERNAME
    gcpw=env.GCPASSWORD
    gcDocker=Artifactory.docker(gcuname, gcpw)
    gcDocker.push(tagNameTest, 'docker-local2')
}
    //stage 'promote'
    def promotionConfig = [
        // Mandatory parameters
        'buildName'          : buildInfo.name,
        'buildNumber'        : buildInfo.number,
        'targetRepo'         : 'docker-prod-local',

        // Optional parameters
        'comment'            : 'this is the promotion comment',
        'sourceRepo'         : 'docker-dev-local',
        'status'             : 'Released',
        'includeDependencies': false,
        'copy'               : false
    ]

    // Promote build
   // artServer.promote promotionConfig

        sh 'sed -E "s/@/$BUILD_NUMBER/" retag.json > retag_out.json'
        sh 'cat retag_out.json'
        def retagstr=curlstr+"api/docker/docker-prod-local/v2/promote' -X POST -H 'Content-Type: application/json' -T retag_out.json"
       // sh retagstr
       */
    }
}
}
