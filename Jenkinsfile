import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles) {
    if (p['publishMethod'] == 'FTP') {
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
    }
  }
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=4ab39ff8-53f4-4766-a57c-c42d3cd3eeab',
           'AZURE_TENANT_ID=82b4b716-e917-4f2d-9a2b-1f298f92e6c3']) {
    stage('init') {
      checkout scm
    }

    stage('build') {
      sh 'mvn clean package'
    }

    stage('deploy') {
      def resourceGroup = 'jenkins-get-started-rg'
      def webAppName = 'WSwebapp'

      // Login to Azure
      az login --service-principal --username 7f577f4c-ef92-4172-922d-fd1bdaac4577 --password 72c4549d-c9de-4e5d-a04c-60cc5d214999 --tenant 82b4b716-e917-4f2d-9a2b-1f298f92e6c3)]) {
        sh "az login --service-principal --username \$AZURE_CREDENTIALS_USR --password \$AZURE_CREDENTIALS_PSW --tenant \$AZURE_CREDENTIALS_TENANT"
        sh "az account set --subscription \$AZURE_SUBSCRIPTION_ID"
      }

      // Get publish profiles
      def pubProfilesJson = sh(script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName --query '[].{publishMethod: publishMethod, publishUrl: publishUrl, userName: userName, userPWD: userPWD}' --output json", returnStdout: true).trim()
      def ftpProfile = getFtpPublishProfile(pubProfilesJson)

      // Upload package
      sh "curl -T target/calculator-1.0.war ${ftpProfile.url}/webapps/ROOT.war -u '${ftpProfile.username}:${ftpProfile.password}'"

      // Log out from Azure
      sh "az logout"
    }
  }
}







