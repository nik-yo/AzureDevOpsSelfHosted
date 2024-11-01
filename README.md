# Azure DevOps Self-Hosted in AWS

The purpose is to host Azure DevOps agent in AWS to better manage cost, performance and control. For better performance, I utilize AWS EC2 Image Builder to create AMI with required tools to build and deploy applications such as .NET SDK, Node.js, Python, AWS CLI, Azure CLI, etc.

## Steps

1. Create a PAT (Personal Access Token) in Azure DevOps.
    - https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/personal-access-token-agent-registration?view=azure-devops

2. Store PAT in AWS Systems Manager Parameter Store with secure string type. This can't be created through Cloudformation.

3. (Optional) Create an agent pool in Azure DevOps. Optionally, we can just use Default agent pool.
    - https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/pools-queues?view=azure-devops&tabs=yaml%2Cbrowser

4. Launch image builder Cloudformation stack using [azuredevops-agent-imagebuilder.yml](infrastructure/aws/azuredevops-agent-imagebuilder.yml) template.

5. Run the image builder pipeline. It will take a while (around 30 minutes for my case) to build. After it is complete, we can get the AMI ID.

6. Launch the autoscaling group Cloudformation stack using [azuredevops-agent-asg.yml](infrastructure/aws/azuredevops-agent-asg.yml) template. This is a slight modification from the template provided by AWS:
    - https://aws.amazon.com/blogs/modernizing-with-aws/using-ec2-auto-scaling-to-manage-azure-pipelines-capacity/

## Stack

- AWS EC2 Image Builder
- AWS Systems Manager
- Amazon EventBridge
- Amazon Autoscaling Group
- Ubuntu 22.04
- Azure DevOps Linux Agent

## Tools Installed in AMI

- Azure DevOps agent
- .NET SDK 6.0 and 8.0
- Git
- Docker
- Helm
- Kubectl
- Jq
- Yq
- Zip
- Unzip
- AWS Cli
- Azure Cli
- PowerShell Core
- Node.js
- Mono
- Python (with Pip and PDM)