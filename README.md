# Quick Start deployment Jira from your own S3 bucket
## Scope
Scope of this document is for quick start deployment Jira from your own S3 bucket with step by step. Also, this will extend including external Ansible repositories into same S3 bucket location and change CloudFormation templated accordingly. 

## Process
NOTE: Make sure you have awscli, git and IDE tool is installed on your local machine.

## Step 1 Create own S3 bucket
Create S3 bucket where you want to create/launch ASI CloudFormation templates

## Step 2 – Download required Binary files.
* Create a folder in your local machine.
* Clone Quick Start CloudFormation templated to your local IDE tool for modifications. 
`git clone --recurse-submodules https://github.com/aws-quickstart/quickstart-atlassian-jira.git`
This will download a folder, named “quickstart-atlassian-jira”

* Also, download required Ansible playbooks from bitbucket so that we can eliminate accessing them from internet in CloudFormation template.
`git clone https://bitbucket.org/atlassian/dc-deployments-automation.git`
This will download a folder, named “dc-deployments-automation”
* Make sure above 2 folders are placed at same location, so that we can push them to S3 directly using AWS CLI as shown below.

![alt text](https://github.com/duggu-reddy/junkFiles/blob/master/file1.png)

## Step 3 – Edit CloudFormation templates. 
* First, lets modify templates to consider our S3 bucket created in our account. 
* Below snippet shows the files and its location to modify S3 Bucket name. 
![alt text](https://github.com/duggu-reddy/junkFiles/blob/master/file2.png)

* In Parameter section of CFNs (CloudFormation), change “QSS3BucketName” parameter value to your S3 bucket name.
![alt text](https://github.com/duggu-reddy/junkFiles/blob/master/file4.png)


##### NOTE: 
* Make sure you have modified only S3 bucket name in all Nested templates.
* Remove parameters “DeploymentAutomationRepository” and “DeploymentAutomationBranch” in Parameter and Resource section so that we can restrict downloading Ansible script from Internet in below mentioned files. 
![alt text](https://github.com/duggu-reddy/junkFiles/blob/master/file5.png)

* Now let’s remove downloading Ansible stuff from Bitbucket and make it available in our won S3 Bucket for which we need to modify “ClusterNodeLaunchConfig” resource “Metadata” section in quickstart-jira-dc.template.yaml template.

Remove below 2 lines. 
![alt text](https://github.com/duggu-reddy/junkFiles/blob/master/file6.png)

And from below snippet
![alt text](https://github.com/duggu-reddy/junkFiles/blob/master/file7.png)

to
![alt text](https://github.com/duggu-reddy/junkFiles/blob/master/file8.png)

* To download required files from S3 Bucket, we need to allow permissions from its Instances-Profile Role. For that we need to attach AWS managed policy in “JiraClusterNodeRole” resource of quickstart-jira-dc.template.yaml template.
![alt text](https://github.com/duggu-reddy/junkFiles/blob/master/file9.png)


##### NOTE: 
* Make sure the nested TemplateURL are placed/referred with exact location, other wise you will face S3 Accedd Denied error. 
* Example: TemplateURL mentioned in /jiraS3deployment/quickstart-atlassian-jira/submodules/quickstart-atlassian-services/templates/quickstart-vpc-for-atlassian-services.yaml calling aws-vpc.template was wrong so make sure it is corrected.
![alt text](https://github.com/duggu-reddy/junkFiles/blob/master/file10.png)


## Step 4 Upload Code into S3 Bucket
* Using AWS CLI, upload entire code into S3 bucket created in Step 1 as shown below
![alt text](https://github.com/duggu-reddy/junkFiles/blob/master/file11.png)

## Step 5 Launch Jira DC with VPC (VPC is optional – based on requirement). 
* Copy file path from S3 bucket to launch CFN stack from CloudFormation service.
![alt text](https://github.com/duggu-reddy/junkFiles/blob/master/file12.png)


* Paste and validate CFN and fill required parameters as shown below. 

![alt text](https://github.com/duggu-reddy/junkFiles/blob/master/file13.png)


