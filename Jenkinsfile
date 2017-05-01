#!groovy

import hudson.model.*
import jenkins.tasks.*

/**
 * Jenkinsfile to manage CD jobs on https://ci.i.blonde.net
 *
 */

/* project properties */
properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '1', artifactNumToKeepStr: '3', daysToKeepStr: '1', numToKeepStr: '3')),
        pipelineTriggers([cron('H/2 * * * *')])
])

node('master') {
	stage ('Get Latest Code'){
		checkout([$class: 'GitSCM', branches: [[name: '*']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'ceb3477d-c313-41bc-9000-b487c88976ed', url: 'https://bitbucket.org/blonde/ngs-tour.git']]])
	}
	stage ('Jekyll Build'){
		sh ('/usr/local/bin/jekyll build')
	}
	stage ('Publish to S3'){

		withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'ngs-audio-tour-aws-s3-read-write',
			usernameVariable: 'AWS_NGS_USERNAME', passwordVariable: 'AWS_NGS_PASSWORD']]) {
				env.AWS_ACCESS_KEY_ID = AWS_NGS_USERNAME
				env.AWS_SECRET_ACCESS_KEY = AWS_NGS_PASSWORD
				AWS_DEFAULT_REGION = 'eu-west-2'

				sh "aws s3 sync '${env.WORKSPACE}'/_site s3://ngs-audio-tour"
			}
		/* Replacing with above due to inconsistent handling of directory root (/)  with this command
		withAWS(region:'eu-west-2',credentials:'ngs-audio-tour-aws-s3-read-write') {
	        s3Upload(file:'_site/', bucket:'ngs-audio-tour', path:'docroot')
		} */
	}
}

