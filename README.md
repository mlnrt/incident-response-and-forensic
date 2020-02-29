# Automated Incident Response and Forensic on AWS
This is an example of an automated incident response and forensic analysis on AWS using AWS GuardDuty, a StepFunction and Lambda functions. The CloudFormation template and code in this repository aim at providing everything to:
* trigger improper security behavior on a web application server, 
* see the event picked-up by GuardDuty
* execute as a response a StepFunction which will control an entire incident response and forensic workflow (details below)
* see the web application auto-heal from the incident
* receive basic snapshot and memory forensic analysis together with all the data to conduct deeper forensic analysis post-incident

# Credits
* This demo is primarily based on the demo released by Ben Potter (https://www.linkedin.com/in/benjipotter/) at AWS Summit London 2018: https://www.youtube.com/watch?v=f_EcwmmXkXk
* The code of his demo is available here : https://github.com/awslabs/aws-security-automation/tree/master/EC2%20Auto%20Clean%20Room%20Forensics
* I also reused and modified a VPC CloudFormation template released by @Levon Becker for Stelligent available here: https://github.com/stelligent/cloudformation_templates/blob/master/infrastructure/vpc.yml
* I also reused shell scripts published by Ryan Holland https://www.linkedin.com/in/rcholland/ and Oliver Cahagne https://www.linkedin.com/in/ocahagne/ on AWS Labs to simulate security breaches to test GuardDuty: https://github.com/awslabs/amazon-guardduty-tester/blob/master/guardduty_tester.sh

# Pre-requisties
* Activate AWS GuardDuty on your AWS account
* Download the two Lambda functions ZIP code (one is for the NginxWebApp YAML template, the other for the Forensic YAML tempalte).

# The demo envrionment
The is the environment deployed by the 4 CloudFormation templates proposed here:
1. The production VPC 
2. The Quarantine VPC
3. The Nginx web application server
4. The incident response and forensic analysis StepFunction and Lambda functions

![](images/architecture-diagram.jpg)

See for the explanation video: https://youtu.be/tUGf4uHOhCA

# The incident response and forensic analysis workflow 
This diagram represents the entire workflow deployed using the last CloudFormation template which uses a StepFunction and Lambda functions to perform the entire incident response and forensic analysis:
* notifying the administrators of the incident and the steps taken
* taking memory dump and EC2 instance snapshot
* installing a forensic instance
* performing memory snapshot and memory dump analysis
* exporting all data collected to S3
* stopping the misbehaving instance

Look at this video for the detailed explanation and the demo: https://youtu.be/Uis8vmlr_WI

![](images/incident-response-workflow.jpg)

# Disclaimer
The code provided in the Lambda functions performing the incident response and forensic analysis is written for AWS Ubuntu Server 18.04 LTS. If you choose a different Linux distribution, you will have to update at the minimum:
* the code in the NginxWebApp-template.yaml file, used to deploy and configure Nginx on the EC2 instance after launch
* the code in the captureMemoryDumpForForensic.py Lambda function to install LiME on the instance and perform the memory dump
* the code in the createForensicInstance.py to create the forensic instance in the Quarantine VPC and install all the tools (e.g. sleuthkit, vloatility...) to perform forensic analysis
* the code in the runSnapshotForensicAnalysis.py to launch commands using Systems Manager to perform the snapshot's forensic analysis
* the code in the runMemoryForensicAnalysis.py to launch commands using Systems Manager to perform the memory dump forensic analysis
