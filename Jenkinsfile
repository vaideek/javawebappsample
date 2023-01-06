import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=d4e53310-d7ea-4386-8e45-a6f2f328f977',
        'AZURE_TENANT_ID=e4e34038-ea1f-4882-b6e8-ccd776459ca0']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'Anshul_RG'
      def webAppName = 'Jenkinswebapp123'
      // login Azure
      withCredentials([usernamePassword(credentialsId: 'mysp', passwordVariable: 'lwM8Q~178js1a2eB66dhBaNHcrDvTjResQ3wYcjW', usernameVariable: 'e5e4bdd3-fabb-4287-b1a6-92f7f5ae3192')]) {
       sh '''
          az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
          az account set -s $AZURE_SUBSCRIPTION_ID
        '''
      }
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      sh 'az logout'
    }
  }
}
