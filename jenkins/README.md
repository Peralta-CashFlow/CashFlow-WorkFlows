# CashFlow Jenkins

This is the [Jenkins](https://www.jenkins.io/) implementation for CashFlow CI/CD automations.

# Summary

- [Setup](#setup)
- [BackEnd Containerization](#backend-containerization)
  - [Jenkins needed file provider](#jenkins-needed-file-provider)
  - [Jenkins needed credentials](#jenkins-needed-credentials)
- [Creating Pipeline](#creating-pipeline)
- [Base Files](#base-files)

# Setup

To Containerize applications you will have to install Jenkins on your machine, please follow the steps
on https://www.jenkins.io/download/.

Install recommended Jenkins plugins and create the first administrative user.

Go to _Manage Jenkins -> Tools -> Maven Installations_ and add Maven version 3.9.10 as "3.9.10".

Go to _Manage Jenkins -> Tools -> JDK Installations -> Add JDK_ and add your JDK version 21 with name "JDK21".

With that you are ready to use Jenkins.

# BackEnd Containerization

This Jenkins pipeline is responsible for getting the main branch code, build the JAR by running _mvn clean install_ 
and with that JAR create a new image version of the application.

## Jenkins needed file provider

For downloading all dependencies from CashFlow we will need to configure a settings.xml file
with GitHub personal access tokens. Follow the step-by-step configuration:

- **Creating File Provider:**

First you will need to add the plugin [Config File Provider](https://plugins.jenkins.io/config-file-provider/) on
your Jenkins. 

After the installation go to _Manage Jenkins -> Managed files -> Add a new Config -> Maven settings.xml_, name/id the new file
as **cash-flow-settings.xml** and for final set the content as bellow:

````xml
<settings>
  <servers>
    <server>
      <id>cashflow</id>
      <username>_GITHUB_USERNAME_</username>
      <password>_GITHUB_TOKEN_</password>
    </server>
  </servers>
</settings>
````

## Jenkins needed credentials

For running this pipeline you will need to create some credentials. For that you will go to _Jenkins Settings -> 
Credentials -> Add credential_

| ID                    | Kind                   | Scope  | Value                                        |
|-----------------------|------------------------|--------|----------------------------------------------|
| github-packages       | Username with password | Global | Username=GHUserName, Password=GHAccessToken* |
| dockerhub-credentials | Username with password | Global | Username=DHUserName, Password=DHAccessToken* |

***Creating GitHub Access Token:**

Access GitHub -> Settings -> Developer Settings -> Personal Access Tokens -> Tokens (classic)
-> Generate new token

Select at least the option read on packages and create the token, be sure to save it on a safe
place so you don't lose it.

***Creating DockerHub Access Token:**

Access DockerHub -> Account Settings -> Personal access token -> Generate new token

Select Read & Write on Access permissions.

# Creating Pipeline

Click on "New Item" on Jenkins, define a name for your pipeline, select "Pipeline" option and click "OK".
After that it will open Configuration tab and on that you will click on "Pipeline" again and select
"Pipeline script from SCM" on _Definition_ option, "Git" on _SCM_, define the repository URL and select 
your GIT credentials.

After this you should click on "Save" and your pipeline is ready to be triggered!

# Base Files

On this folder we have two base files "Dockerfile" and "Jenkinsfile" that will be needed on all of the
repositories that will implement docker deploy with Jenkins.

Remember to update API name on Jenkinsfile.