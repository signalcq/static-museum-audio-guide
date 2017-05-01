#!groovy

import hudson.model.*
import jenkins.tasks.*

/**
 * Jenkinsfile to manage CD jobs on https://ci.i.blonde.net
 *
 */

/* project properties */
properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '5', numToKeepStr: '5')),
])


node('master') {
	stage ('Get Latest Code'){
		scm checkout
	}
	stage ('Publish to S3'){
		withAWS(credentials:'ngs-audio-tour-aws-s3-read-write') {
        		s3Upload(file:'_site', bucket:'ngs-audio-tour', path:'/')
		}
	}
}

