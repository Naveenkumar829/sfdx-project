#!groovy

import groovy.json.JsonSlurperClassic

node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="test/${BUILD_NUMBER}"
	def SFDC_USERNAME
    def HUB_ORG=env.HUB_ORG_DH
    def SFDC_HOST=env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID=env.JWT_KEY_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH
   
   println 'KEY IS'
   println JWT_KEY_CRED_ID
   println HUB_ORG
   println SFDC_HOST
   println CONNECTED_APP_CONSUMER_KEY
   def toolbelt = tool 'toolbelt'


    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

    stage('checkout source') {
        checkout scm
    }


    // -------------------------------------------------------------------------
    // Run all the enclosed stages with access to the Salesforce
    // JWT key credentials.
    // -------------------------------------------------------------------------
    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]){
	stage('Deploy Code') {
    
	if(isUnix()) {
	rc = sh returnstatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
	}else{
	rc = bat returnstatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
	}
	if (rc != 0) { error 'hub org authorization failed' )
	println rc
	
	//need to pull out assigned username
	if (isUnix()) {
	rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifesty/. -u ${HUB_ORG}"
	}else{
	rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:mdapi:deploy -d manifesty/. -u ${HUB_ORG}"
	}
	printf rmsg
	println ('Hello from a job DSL Script!')
	println(rmsg)
	}
	}
	}
	
	
