<h1>Push Docker images to Amazon ECR using YAML based Azure Pipelines</h1>


<h2>Description</h2>
I detail all the steps required to push a docker image to AWS ECR usinf Azure pipelines and Docker CLI 
<img src="https://i.imgur.com/LWgnl6J.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>


<br />

<h2>Environments Used </h2>

- <b>AWS ECR</b>
- <b>Docker</b>
- <b>Azure pipelines</b> 

<h2>Walk-through:</h2>

<p align="center">
Pre-requisite 1: I followed the documentation available at aws-docs-image-push-iam-policy and created an IAM policy from the iam-policy.json file with appropriate permissions. <br/>
<img src="https://i.imgur.com/ZJqh1zy.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<br />
<br />
<p align="center">
Pre-requisite 2: Although I could have created the Amazon ECR repository in advance, I addressed that in the azure-pipelines.yml file via a check. I have an image of the code from my GitHub repository below. I checked to see if a repository exists, and if not, create it. Hence, I required the two additional permissions in the iam-policy.json file:  <br/>
<img src="https://i.imgur.com/3uNJyfn.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<br />
The following steps were to automate creating and pushing a docker image into the Amazon ECR repository. However, since I used the azure-pipelines.yml file, there were a couple more steps to follow. Variables:
I created a library variable to securely store the access_key, secret_key and aws_account_number associated with the IAM user. In addition, this IAM user had the iam-policy attached to communicate with the Amazon ECR repository.
<p align="center">
Install AWS PowerShell Tools:
I used the AWS Tools for PowerShell and hence automated the process of installing all the required modules. You may read more about that at Install AWS.Tools. The InstallAWSTools.ps1 PowerShell script in my GitHub repo has all the details.
<p align="center">
Set AWS Credentials:
The next step was to create a local profile that was referenced by the subsequent steps to interact with the AWS cloud API. The details are available at specifying-your-aws-credentials.
<p align="center">
Check and create an Amazon ECR repository:
I mentioned that above under pre-requisites. Once an AWS profile was available, I checked if an Amazon ECR repository existed before creating a new one. Idempotency is at play here.
<p align="center">
Remove AWS Credentials:
The last step in the azure-pipelines.yml file was to remove the credentials. Such that even if any of the above steps failed due to specific errors, it would continue due to the condition: always() rule. You may read more at yaml-specify-condition.
<br />
<br />
Step 1: Create the application code that will be hosted in a container
For this project, I used a simple static app <br/>
<br />
<br />
Step 2: Create and tag a docker image. The docker file has the details.  <br/>
<img src="https://i.imgur.com/FrlxnXZ.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<br />
<br />
<p align="center">
As you can see from the image, the docker image is based on an alpine image where I copied the files from the app folder, run npm install, and provide an ENTRYPOINT.
However, before docker build, I was required to authenticate docker CLI to my default registry  -AWS ECR. That was possible using Get-ECRLoginCommand.  <br/>
<img src="https://i.imgur.com/yYqkAGh.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<br />
The build and tag steps are relatively self-explanatory.<br />
<br />
<img src="https://i.imgur.com/drsdWAP.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p align="center">
Step 3: Upload the docker image into an image repository. I used the docker push command for this step <br/>
<img src="https://i.imgur.com/Pfk6nqd.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<br />
And once I completed all the docker CLI steps, I added the docker logout step to close the session.<br />
<br />
<img src="https://i.imgur.com/NcB5kVi.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>
<br />
And as I mentioned above, the last step in the azure-pipelines.yml file was to remove the profile that stored the AWS Credentials. All the AWS.Tools used the profile for PowerShell command using the ProfileName flag.<br />
<br />  
<img src="https://i.imgur.com/chYjoTE.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<br />
<br />
And that brings us to the end of this project. I know that there are a lot of detailed pieces of information here, and I created it so that you can find all the relevant information here..  <br/>
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
