// Deployment template for Jenkins
// Author: Jim Applegate
//
// openartifacts.com
//
pipeline {
	agent any

	environment {
		SITEID = "oac"
		PATH = "/Users/jimappleg8/gems/bin:/usr/local/bin:${env.PATH}"
		GEM_HOME = "/Users/jimappleg8/gems"
	}

	stages {
		stage("bundle-build") {
		// build the website
			steps {
				dir ("${env.SITEID}docs") {
					sh "env"
					sh "/usr/bin/bundle exec jekyll build"
				}
				sh "cp -f \"${WORKSPACE}/${env.SITEID}docs/.htaccess\" \"${WORKSPACE}/${env.SITEID}docs/_site/.htaccess\""
			}
		}		
		stage("publish-files") {
			steps {
				// prepare the live server to receive files by changing the owner
				sh "ssh webadmin@208.68.39.51 \"sudo chown -R webadmin:webadmin /var/opt/httpd/${env.SITEID}docs/\""
				// copy files from control server to live
				sh "rsync -rav -e ssh --delete \"${WORKSPACE}/${env.SITEID}docs/\" webadmin@208.68.39.51:/var/opt/httpd/${env.SITEID}docs/"
				// fix the owner/permissions on the live server
				sh "ssh webadmin@208.68.39.51 \"sudo chown -R apache:${env.SITEID}-web /var/opt/httpd/${env.SITEID}docs/\""
				sh "ssh webadmin@208.68.39.51 \"sudo chmod -R g+w /var/opt/httpd/${env.SITEID}docs/\""
				sh "ssh webadmin@208.68.39.51 \"sudo find /var/opt/httpd/${env.SITEID}docs/ -type d -exec chmod g+s {} \\;\""
			}
		}
	}
}