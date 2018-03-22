# ELK install on AWS

## Directory structure

* ansible
	* Contains the Ansible playbooks and other related files necessary to install ELK
* cloudformation
	* Contains the json formatted Cloudformation template to create a Stack on Amazon AWS. The stack contains two servers: a bare Amazon Linux server to host ELK, and a Jenkins server that is prepackaged with a job that executes the ELK install
* jenkins
	* Contains the Jenkinsfile Groovy script that desctibes the pipeline job that executes the Ansible scripts to install ELK

## Cloudformation template

The Cloudformation template sets up a stack containing all of the necessary components for a Jenkins/Ansible server to install ELK on a target. It creates a Virtual Private Cloud, Subnet, Network ACL, Gateway, Security Group, and two servers with public IP addresses. It outputs the URLs of the servers.

## Ansible scripts

The Ansible scripts install a bare-bones ELK stack with minimal configuration. Playbooks exist to install Java 8 (and remove Java 7), install Elasticsearch (configuring java heap size to fit on a t2.small instance), install Logstash, and install Kibana. 

## Jenkins AMI

The Jenkins AMI is prepackaged with a job that executes the ELK install. The Jenkins job (named aws-install-elk) accepts the IP address or DNS name of the target instance as a parameter. It updates the ansible hosts file with the supplied target parameter, then executes each playbook in turn.

## Notes

* The Jenkins job sets StrictHostKeyChecking=no, but it assumes a specific .pem file for SSH connections. When creating a stack from the Cloudformation template the key pair that Jenkins expects may be used, or alternatively a different key pair may be used. If a different key pair is used, the new key pair will need to be copied to the Jenkins installation .ssh directory, and the jenkins user will need to chown the private key.

* Jenkins is set up to download everything from this github repo. Minimal data is stored in Jenkins itself. If the repository permissions are changed to private, the repository disappears, or an internet connection is lost, the build will fail.