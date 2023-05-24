<a href="https://www.instagram.com/ttomasferrari/">
    <img align="right" alt="Abhishek's Instagram" width="22px" src="https://raw.githubusercontent.com/hussainweb/hussainweb/main/icons/instagram.png" />
</a>
<a href="https://twitter.com/tomasferrari">
    <img align="right" alt="Abhishek Naidu | Twitter" width="22px" src="https://raw.githubusercontent.com/peterthehan/peterthehan/master/assets/twitter.svg" />
</a>
<a href="https://www.linkedin.com/in/tomas-ferrari-devops/">
    <img align="right" alt="Abhishek's LinkedIN" width="22px" src="https://raw.githubusercontent.com/peterthehan/peterthehan/master/assets/linkedin.svg" />
</a>
<p align="right">
    <a  href="/docs/readme_es.md">Versión en Español</a>
</p>

<p title="All The Things" align="center"> <img src="https://i.imgur.com/BbBEFEE.jpg"> </p>

# **INDEX**

- [Introduction](#introduction)
  - [Prerequisites](#prerequisites)
  - [What we'll be doing](#what-well-be-doing)
  - [Tools we'll be using](#tools-well-be-using)
  - [Disclaimer](#disclaimer)
- [Local Setup](#local-setup)
- [Azure DevOps Setup](#azure-devops-setup)
  - [Create project](#create-project)
  - [Install required plugins](#install-required-plugins)
  - [Get your AWS keys](#get-your-aws-keys)
  - [Create AWS service connection](#create-aws-service-connection)
  - [Create DockerHub service connection](#create-dockerhub-service-connection)
  - [Create AWS-keys variable group](#create-aws-keys-variable-group)
  - [Allow push to GitHub](#allow-pushes-to-github)
  - [Create an Azure self-hosted agent](#optional-create-an-azure-self-hosted-agent)
- [AWS Infrastructure Deployment Pipeline](#aws-infrastructure-deployment-pipeline)
  - [Description](#description)
  - [Instructions](#instructions)
- [ArgoCD Deployment Pipeline](#argocd-deployment-pipeline)
  - [Description](#description-1)
  - [Instructions](#instructions-1)
- [Application Build & Deploy Pipeline](#application-build--deploy-pipeline)
  - [Description](#description-2)
  - [Instructions](#instruction2)
- [Destroy All The Things Pipeline](#destroy-all-the-things-pipeline)
  - [Description](#description-3)
  - [Instructions](#instructions-3)
- [Epilogue](#epilogue)

<br/>

# **INTRODUCTION**

I believe in a world where all that's expected of me is to enjoy life, lay on the couch, play COD and have exitential crises.

I wish I could automate cooking, cleaning, working, doing taxes, making friends, dating, writing READMEs... Hell, I'd even automate playing with my stupid kids if I could.

But technology hasn't quite catched up to my level of laziness yet, so I've taken some inspiration from Thanos and said ["Fine... I'll do it myself"](https://www.youtube.com/watch?v=EzWNBmjyv7Y).

Here's my attempt at making the world a better place. People in the future will look back at heros like me and enjoy their time playing video games and fighting the war against AI, in peace.

<br/>

## Prerequisites

- [Git installed](https://www.python.org/downloads/)
- [Python3 installed](https://www.python.org/downloads/)
- [Active GitHub account](https://github.com/)
- [Active DockerHub account](https://hub.docker.com/)
- [Active AWS account](https://aws.amazon.com/)
- [Active Azure DevOps account](https://azure.microsoft.com/en-us/free/)

<br/>

## What we'll be doing

INCLUIR DIAGRAMA?????

The purpose of this repo is not to give you an in depth explanation of the tools we are using, but to demonstrate how easy it can be to deploy a whole infrastructure and how the tools can interact with each other to make them as efficient as posible.

I want to show you the power of IaC (Infrastructure as Code), Gitops and CI/CD (Continuous Integration/Continuous Deployment), while providing some ideas on how all of these methodologies can be merged for unlimited power.

<br/>

## Tools we'll be using

For each step of the process, I’ve chosen to use the best tool in its field. And yeah... you might be thinking, "best" is a subjective term, right? Well... not here. This is MY repo! My opinions here are TRUTHS!!

Ok, now that that's out of the way...

- Code Versioning -> Git
- Source Code Managment -> GitHub
- Cloud Infrastructure -> Amazon Web Services
- Infrastructure as Code -> Terraform
- Containerization -> Docker
- Container Orchestration -> Kubernetes
- Continuous Integration -> Azure DevOps
- Continuous Deployment -> Helm & ArgoCD
- Scripting -> Python
  <br/>

<p title="Logos Banner" align="center"> <img  src="https://i.imgur.com/Jd0Jve8.png"> </p>

<br/>

## Disclaimer

Some things could have been further automatized but I prioritized modularization and separation of concerns.<br>

For example, the EKS cluster could have been deployed with ArgoCD installed in one pipeline, but I wanted to have them separated so that each module is focused on it's specific task, making each of them more recyclable.

Let's begin...

<br/>
<br/>
<p title="Automation Everywhere" align="center"> <img width="460" src="https://i.imgur.com/xSmJv0k.png"> </p>
<br/>

---

<br/>

# **LOCAL SETUP**

In order to turn this whole deployment into your own thing, we need to do some initial setup:

1. Fork this repo. Keep the repository name "automate-all-the-things".
1. Clone the repo from your fork:

```bash
git clone https://github.com/<your-username>/automate-all-the-things.git
```

2. Move into the directory:

```bash
cd automate-all-the-things
```

2. Run the initial setup script. Come back when you are done:

```bash
python3 python/initial-setup.py
```

4. Hope you enjoyed the welcome script! Now push your customized repo to GitHub:

```bash
git add -A
git commit -m "customized repo"
git push
```

5. Awesome! You can now proceed with the Azure DevOps setup.

<br/>
<br/>
<p title="Evil Kermit" align="center"> <img width="650" src="https://i.imgur.com/pIGcI2d.jpg"> </p>
<br/>
<br/>

# **AZURE DEVOPS SETUP**

Before creating our pipelines we need to get a few things set up:<br>

## Create project

1. Sign in [Azure DevOps](https://dev.azure.com/).
2. Go to "New project" on the top-right.
3. Write the name for your project and under "Visibility" select "Private".
4. Click "Create".

<br/>

## Install required plugins

These plugins are required for the pipelines we'll be creating. Click on "Get it free", select your organization and then click "Install".

1. Install [Terraform Tasks plugin](https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform) for Azure Pipelines
1. Install [AWS Toolkit plugin](https://marketplace.visualstudio.com/items?itemName=AmazonWebServices.aws-vsts-tools) for Azure Pipelines

<br/>

## Get your AWS keys

These will be required for Azure DevOps to connect to your AWS account.

1. Open the IAM console at https://console.aws.amazon.com/iam/.
2. On the search bar look up "IAM".
3. On the IAM dashboard, select "Users" on the left side menu. *If you are root user and haven't created any users, you'll find the "Create access key" option on IAM > My security credentials. You should know that **creating Access Keys for the root user is a bad security practice**. If you choose to proceed anyway, click on "Create access key" and skip to point 6*.
4. Choose your IAM user name (not the check box).
5. Open the Security credentials tab, and then choose "Create access key".
6. To see the new access key, choose Show. Your credentials resemble the following:

- Access key ID: AKIAIOSFODNN7EXAMPLE<br>
- Secret access key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

7. Copy and save these somewhere safe.

<br/>

## Create AWS service connection

This service connection is required for our Azure DevOps pipelines to interact with AWS.

1. Go back to Azure DevOps and open your project.
2. Go to Project settings on the left side menu (bottom-left corner).
3. On the left side menu, under "Pipelines", select "Service connections".
4. Click on "Create service connection".
5. Select AWS, click "Next".
6. Paste your Access Key ID and Secret Access Key.
7. Under "Service connection name", write "aws".
8. Select the "Grant access permission to all pipelines" option.
9. Save.

<br/>

## Create DockerHub service connection

This service connection is required for our Azure DevOps pipelines to be able to push images to your DockerHub registry.

1. While on the "Service connections" screen, click on "New service connection" on the top-right.
5. Select "Docker Registry", click "Next".
6. Under "Registry type" select "Docker Hub".
7. Input your Docker ID and Password.
8. Under "Service connection name", write "dockerhub".
9. Select the "Grant access permission to all pipelines" option.
10. Click on "Verify and save".

<br/>

## Create AWS keys variable group

These are needed for Terraform to be able to deploy our AWS infrastructure.

1. On the left side menu under "Pipelines" go to "Library"
2. Click on "+ Variable group".
3. Under "Variable group name" write "aws-keys".
4. Add the following variables pasting on the "Value" field the keys you used to create the AWS service connection:
- aws_access_key_id
- aws_secret_access_key
5. Click on the lock icon on each variable.
7. Click on "Save".
7. Click on "Pipeline permissions" and give it "Open access". This means all our pipelines will be able to use these variables.
<p title="Guide" align="center"> <img width="700" src="https://i.imgur.com/aMzTx49.jpg"> </p>

<br/>

## Allow push to GitHub

We will need to push changes to the GitHub repo. By default, repositories are allowed to be read from but not written to, so we need to do a little extra configuration.

1. Go to Project setting > Repositories (under Repos) > Select your repo > Security tab > Users > <your-project-name> Build Service (<your-org-name>)
   CHEKEAR CUALES SON REALMENTE NECESARIAS!!!!!!!!!!!!!!!!!!!!!!!!
2. "Bypass policies when pushing", "Contribute", "Create Tag" and "Read" should be set to "Allow".
<p title="Guide" align="center"> <img width="700" src="https://i.imgur.com/NzYh5KJ.png"> </p>

<br/>

<!-- NO HACE FALTA PORQ SE CREA SOLA CON EL NOMBRE DE USERNEAME DE GITHUB -->
<!-- ### GitHub
4. On the Service connectionlick on "New service connection" .
5. Select AWS.
6. Paste your Access Key ID and Secret Access Key.
7. Under "Service connection name", write "aws".
8. Select the "Grant access permission to all pipelines" option.
9. Save. -->

## (Optional) Create an Azure self-hosted agent

**If you have a hosted parallelism, you can skip this step.**<br/>

A hosted parallelism basically means that Azure will spin up a server in which to run your pipelines. You can purchase one or you can request a free parallelism by filling out [this form](https://aka.ms/azpipelines-parallelism-request).

If you don't have a hosted parallelism, you will have to run the pipeline in a **self-hosted agent**.
This means you'll install an Azure DevOps Agent on your local machine, which will recieve and execute the pipeline jobs.

To install a self-hosted agent on your machine, you can follow the official documentation [here](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/agents?view=azure-devops&tabs=browser#install).

<br/>
<br/>
<p title="We Are Not The Same" align="center"> <img width="460" src="https://i.imgur.com/E0s8TW6.png"> </p>
<br/>
<br/>

---

<br/>
<br/>

<!-- # **PIPELINES** -->
<!-- # AWS Infrastructure Deployment Pipeline -->

# AWS INFRASTRUCTURE DEPLOYMENT PIPELINE

<!-- ### **Explanation** -->

## Description

Our first pipeline, the one that will provide us with all the necessary infrastucture.

What does this pipeline do? If you take a look at the [00-deploy-infra.yml](azure-devops/00-deploy-infra.yml) file, you'll see that the first thing we do is use the Terraform plugin we previously installed to deploy a S3 Bucket and DynamoDB table. These two resources will allow us to store our terraform state remotely and give it locking functionality.<br/>

Why do we need to store our tf state remotely and locking it? Well, this is probably not necessary for this excercise but it's a best practice when working on a team.<br>
Storing it remotely means that everyone on the team can access and work with the same state file, and locking it means that only one person can access it at a time, this prevents state conflicts.

Before we proceed with deploying out actual infrastructure, we'll move the state file to the /terraform/aws/ directory, so our backend resources (the Bucket and DynamoDB Table) will also be tracked as part of our whole infrastructure. If you want to understand how this works, I suggest you watch [this video](https://youtu.be/7xngnjfIlK4?t=2483) where Sid from [DevOps Directive](https://www.youtube.com/@DevOpsDirective) explains it better than I ever could.

Now that's everything is set, we will finally deploy our infra!

So what is our infra? It's mainly the networking resources and the EKS cluster, along with an AWS Load Balancer Controller which will act as our Kubernetes Ingress Controller.

Having this AWS Load Balancer Controller means that for every Ingress resource we create in our cluster, an AWS Application Load Balancer will be automatically created. This is the native way to do it in EKS and it has a lot to benefits, but it creates an issue for us.<br>
We want to track everything in our infra as IaC, but these automatically created Application Load Balancers won't be tracked in our Terraform... No worries, we'll take care of this issue in the Destroy Everything Pipeline.<br>
For more info on the AWS Load Balancer Controller you can watch [this excellent video](https://youtu.be/ZfjpWOC5eoE) by [Anton Putra](https://www.youtube.com/@AntonPutra).

If you want to know exactly what is being deployed, you can check out the [terraform files](/terraform/aws).

<br/>

<!-- ### **Instructions** -->

## Instructions

1. On your Azure DevOps project, go to "Pipelines" on the left side menu.
2. Select "Pipelines" under "Pipelines" on the left side menu.
3. Click on "Create Pipeline".
4. Select "Github".
5. You might need to go through the GitHub authorization process, go ahead and click the green button.
6. Select the repo, it should be "your-github-username/automate-all-the-things"
7. You might need to click more green buttons to allow Azure DevOps to interact with GitHub, go ahead.
8. Select "Existing Azure Pipelines YAML file".
9. Under "Branch" select "main" and under "Path" select "/azure-devops/00-deploy-infra.yml". Click "Continue".
10. *If you have hosted parallelism skip to point 11*. **If you DON'T have a hosted parallelism**, you need to tell Azure DevOps to use your [**self-hosted agent**](#optional-create-an-azure-self-hosted-agent). In order to do this, you'll need to go to the repo and modify the [00-deploy-infra.yml file](azure-devops/00-deploy-infra.yml).<br>
    Under "pool" you need to edit it so that it looks like this:

```yaml
pool:
  # vmImage: 'ubuntu-latest'
  name: <agent-pool-name> # Insert here the name of the agent pool you created
  demands:
    - agent.name -equals <agent-name> # Insert here the name of the agent you created
```

11. Click on "Run".

<br/>
<br/>
<p title="Pleasure" align="center"> <img width="460" src="https://i.imgur.com/n5lNz3z.jpg"> </p>
<br/>
<br/>

<!-- 12. You might get a warning saying "This pipeline needs permission to access a resource before this run can continue". Click on "View" and "Permit". -->

<!-- <br/>
<p title="Guide" align="center"> <img width="700" src="https://i.imgur.com/UtZyCCe.png"> </p>
<br/> -->

<!-- ## EKS Deployment Pipeline VIEJO

2. Go to "Pipelines" under "Pipelines" on the left side menu.
3. Click on "New pipeline".
4. Select "GitHub".
6. Select the repo, it should be "<your-github-username>/automate-all-the-things"
6. Select "Existing Azure Pipelines YAML file".
9. Under "Branch" select "main" and under "Path" select "/azure-devops/01-deploy-eks.yml". Click "Continue".
11. Click on "Run". -->
<!-- 9. Rename the pipeline to "deploy-eks". On the Pipelines screen, click on the three-dot menu to see the Rename/move option. -->
<!-- 10. When it's finished, the KubeConfig file will be exported as an artifact. You'll find it in the pipeline run screen. Download it, NO PARA SERVICE CONNECTION PERO SI PARA TOCAR KUBECTL A MANO DESDE LOCAL we'll need it to create the Kubernetes service connection. -->

<!-- <br>

#### Configure AWS CLI and EKS Cluster
To check that everything went OK we will connect to our cluster from our local machine. Use the following commands, you'll need to input your AWS info. When prompted for "Default output format" just press enter.
```bash
aws configure
aws eks update-kubeconfig --name <your-app-name>-cluster --region <your-aws-region>
```
**REMEMBER**: This is only to SEE our resources. Creating or deleting resources is strictly prohibited. We're not some cavemen using kubectl create/delete. We are gentlemen, we modify our infrastrucure with **GitOps**.

To visualize our resource we can now use:
```bash
kubectl get all --all-namespaces
```

INSTERT WINNIE POOH MEME
<br> -->

<!-- #### Create AWS-Keys Variable Group
These are needed for Helm to be able to connect to our EKS Cluster and deploy ArgoCD.

1. On the left side menu under "Pipelines" go to "Library"
2. Click on "+ Variable group".
3. Under "Variable group name" write "aws-keys".
4. Add the following variables pasting on each the same keys you used to create the AWS service connection:
- aws_access_key_id
- aws_secret_access_key
5. Click on the lock icon on each variable so that they are treated as secrets.
6. Save. -->

<!-- #### Create K8S Service Connection

1. Go to "Project settings" on the left side menu (bottom-left corner).
2. On the left side menu, under "Pipelines", select "Service connections".
3. Click on "New service connection" (top-right).
4. Select "Kubernetes" and click "Next".
5. Under "Authentication method" select "KubeConfig".
6. Paste the contents of the kubeconfig previously downloaded inside the box.
7. Select "Accept untrusted certificates".
7. Under "Service connection name", write "k8s".
8. Select "Grant access permission to all pipelines".
8. Click on "Verify and save". -->

<!-- ## ArgoCD Deployment Pipeline -->

# ARGOCD DEPLOYMENT PIPELINE

<!-- ### **Explanation** -->

## Description

CHEKEAR SI ES NECESARIO EL PASO EN EL PIPELINE DE CREAR EL .aws/config

We won't go into what ArgoCD is, for that you have [this video](https://youtu.be/MeU5_k9ssrs) by the #1 DevOps youtuber, Nana from [TechWorld with Nana](https://www.youtube.com/@TechWorldwithNana).

This pipeline will use the [ArgoCD Helm Chart](helm/argo-cd/) in our repo to deploy ArgoCD into our EKS.<br>
The first thing it will do is run the necessary tasks to connect to our the cluster. After this, ArgoCD will be installed, along with it's Ingress.

As I explained before, the Ingress will automatically create an AWS Application Load Balancer. This LB takes a few moments to become active, so our pipeline will wait until it is ready.
When it's ready, the pipeline will get it's URL and admin account password. These will be exported as an artifact.

Finally, it will create the ArgoCD [application resource](argo-cd/application.yaml) for our app, which will be watching the [/helm/my-app](helm/my-app) directory in our repo, and automatically create all the resources it finds and apply any future changes me make there.

<br/>

## Instructions

1. Go to "Pipelines" under "Pipelines" on the left side menu.
2. Click on "New pipeline".
3. Select "GitHub".
4. Select the repo, it should be "your-github-username/automate-all-the-things"
5. Select "Existing Azure Pipelines YAML file".
6. Under "Branch" select "main" and under "Path" select "/azure-devops/01-deploy-argocd.yml". Click "Continue".
7. If you DON'T have a hosted parallelism, you'll need to do the same thing as in point 10 from the previous pipeline.
8. Click on "Run".
9. When it's done, the access file will be exported as an artifact. You'll find it in the pipeline run screen. Download it to see the URL and credentials.
<p title="Guide" align="center"> <img width="700" src="https://i.imgur.com/UtZyCCe.png"> </p>

10. You can now access the ArgoCD UI, where you should find one application running but in an "unhealthy" state. This is because we haven't built our app and pushed it to DockerHub yet. Let's take care of that next.

<!-- 12. You might get a warning saying "This pipeline needs permission to access a resource before this run can continue". Click on "View" and "Permit". -->
<!-- 9. Rename the pipeline to "deploy-argocd". On the Pipelines screen, click on the three-dot menu to see the Rename/move option. -->

<br/>
<br/>
<p title="Gitops Chills" align="center"> <img width="460" src="https://i.imgur.com/kGQUUTw.jpg"> </p>
<br/>
<br/>

<!-- ## Application Build & Deploy Pipeline -->

# APPLICATION BUILD & DEPLOY PIPELINE

<!-- ### **Explanation** -->

## Description

AGREGAR Q SE CORRE CADA COMMIT AUTOMTICAMENTE!!!!!!
We are almost there! In this pipeline we will build and deploy our app.

The [my-app directory](my-app) on the repo is meant to represent an application code repository. Here you'll find the application code files and the corresponding Dockerfile. You could theoretically replace the contents of the my-app directory with the code and Dockerfile for any other app and it should still work.

There's two parts to this pipeline:

On the Build part we will use Docker to build a container image from the Dockerfile, tag it with the number of the pipeline run and push it to your DockerHub registry.

On the Deploy part, we will checkout the repo and modify the [values.yaml file](helm/my-app/values.yaml) on the [helm/my-app](helm/my-app) directory and push the change to GitHub. [But why?](https://i.gifer.com/2Gg.gif)<br>
Remember how we just pushed the image to DockerHub with the new tag? And remember how ArgoCD is watching the helm/my-app directory? Well, this is how we tell ArgoCD that a new version is available and should be deployed. We modify the image.tag value in the values.yaml file and wait for ArgoCD to apply the changes.

[This is how gentlemen manage their K8S resources](https://i.imgur.com/2Xntz2P.jpg). We are not some cavemen creating and deleting stuff manually with kubectl. We manage our infrastucture with **GitOps**.

If you need to modify other things, let's say, the contents of the ConfigMap, then you'd clone the repo, make your changes, push to GitHub, and again, wait for ArgoCD to apply the changes.

<br/>

## Instructions

1. Go to "Pipelines" under "Pipelines" on the left side menu.
2. Click on "New pipeline".
3. Select "GitHub".
4. Select the repo, it should be "your-github-username/automate-all-the-things"
5. Select "Existing Azure Pipelines YAML file".
6. Under "Branch" select "main" and under "Path" select "/azure-devops/02-build-and-deploy-app.yml". Click "Continue".
7. Click on "Run".

<br/>
<br/>
<p title="Momoa & Cavill" align="center"> <img width="460" src="https://i.imgur.com/2lJ6xLl.jpg"> </p>
<br/>
<br/>

<!-- ## Application Deployment Pipeline
2. Go to "Pipelines" under "Pipelines" on the left side menu.
3. Click on "New pipeline".
4. Select "GitHub".
6. Select the repo, it should be "<your-github-username>/automate-all-the-things"
6. Select "Existing Azure Pipelines YAML file".
9. Under "Branch" select "main" and under "Path" select "/azure-devops/04-dep-and-deploy-app.yml". Click "Continue".
11. Click on "Run". -->

# DESTROY ALL THE THINGS PIPELINE

## Description

<!-- ### **Instructions** -->

## Instructions

PRIMERO BORRAR TODOS LOS INGRESS!!!!!!!
uncomment vars
complete values in .tfvars
tf init
tf destroy

Will fail cause bucket and dynamo dont excist any more

<br/>
<br/>
<p title="Seagull" align="center"> <img width="460" src="https://i.imgur.com/2Z8qEvC.png"> </p>
<br/>
<br/>

---

<br/>

# EPILOGUE

Our journey has come to an end...

DOCKER BEST PRACTICES

AGREGAR AMBIENTES PARA MY APP

TF BEST PRACTICES

AGREGAR CHEKEO DE Q INPUTS EN PYTHON SCRIPT SEAN VALIDOS, SOLO TEXTO, NROS y - y \_

12 FACTOR APP
PASAR TODO A PYTHON SCRIPT

EXPLICAR PORQ USAMOS REMOTE BACKEDN PARA TFSTATE.

EL NOMBRE DEL BACKEND S3 y DYNAMODB ESTAN HARDCODEADOS EN EL BACKEND DE aws-resources, HAY Q GUARDARLOS EN VARIABLE DE ALGUNA FORMA

<br/>
<p title="Anakin" align="center"> <img width="460" src="https://i.imgur.com/tup8Ocu.jpg"> </p>
<br/>
<br/>
<p title="Scroll Of Truth" align="center"> <img width="460" src="https://i.imgur.com/yjpOvlM.jpg"> </p>
<br/>

<br/>
<p title="Thinking About Another Woman" align="center"> <img width="460" src="https://i.imgur.com/akNhnrh.jpg"> </p>
<br/>

<br/>
<p title="Wolverine & Nana" align="center"> <img width="460" src="https://i.imgur.com/dz0RdX5.png"> </p>
<br/>

DESCARTADO!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

# (Optional) GET GITHUB PERSONAL ACCESS TOKEN

**_If the repo you are using is public skip this step._**<br/>

In your Github account:

1. In the upper-right corner of any page, click your profile photo, then click Settings.
2. In the left sidebar, click Developer settings.
3. In the left sidebar, click Personal access tokens.
4. Click Generate new token.
5. In the "Note" field, give your token a descriptive name.
6. To give your token an expiration, select Expiration, then choose a default option or click Custom to enter a date.
7. Select the scopes you'd like to grant this token. To use your token to access repositories from the command line, select repo. A token with no assigned scopes can only access public information. For more information, see "Scopes for OAuth Apps".
8. Click Generate token.
9. Copy the new token to your clipboard, click.

Set as environment variable

```bash
export AZURE_DEVOPS_EXT_GITHUB_PAT=<your-github-pat>
```

# 1. CREATE PIPELINE

## 1.1 Azure CLI

a. Install [azure cli](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)

```bash
sudo curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

b. Sign in:

```bash
az login
```

## 1.2 Azure DevOps CLI

a. Add the [Azure DevOps extension](https://learn.microsoft.com/en-us/azure/devops/cli/?view=azure-devops)

```bash
az extension add --name azure-devops
```

## 1.3 Project & Pipeline

a. Create project

```bash
az devops project create --name automate-all-the-things --org https://dev.azure.com/tomasferrari --verbose
```

b. Set organization and project as defaults

```bash
az devops configure --defaults  organization=https://dev.azure.com/tomasferrari project=automate-all-the-things
```

c. Create service-connection to github

```bash
az devops service-endpoint github create --name github-sc --github-url https://github.com/tferrari92/automate-all-the-things.git
```

d. Create pipeline

```bash
az pipelines create --name create-bucket --repository https://github.com/tferrari92/automate-all-the-things.git --branch main --yml-path azure-devops/deploy-aws-resources.yml --service-connection github-sc
```

<a href="https://www.youtube.com/watch?v=EzWNBmjyv7Y" target="_blank">"Fine... I'll do it myself"</a>.
