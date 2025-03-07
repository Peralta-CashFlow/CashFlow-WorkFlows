# CashFlow-Workflows

This repository is a center point for all CashFlow workflows, here we have reusable workflows that should be used on the other repositories.

Centralizing the workflows will make it easier for maintenance and problem resolution as we will only need to update the files here and it will be reflected in all other repositories.

# Workflows

Let's se all the available workflows, check what they are responsible for and understand how to use it.

## Sonar - Java

The workflow ["_sonar-analysis.v1.yml_"](https://github.com/Peralta-CashFlow/CashFlow-WorkFlows/blob/main/.github/workflows/sonar-analysis-v1.yml) is responsible for running [Sonar](https://www.sonarsource.com/) scanning on Java code, it will analyse if:

- Unit test coverage is greater than 80%;
- No new bugs were introduced;
- No new vulnerabilities were introduced;
- New code duplicated lines is less than or equal 3.0%;

## Conventional Commit

The workflow ["_conventional-commit-v1.yml_"](https://github.com/Peralta-CashFlow/CashFlow-WorkFlows/blob/main/.github/workflows/conventional-commit-v1.yml) is responsible for scanning a Pull Request commits and verify if all of them are following [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) standards.

If all commits are fine you will be able to merge your PR, if not it will fail the workflow.

## Deploy on GitHub Pages - React

The workflow ["_deploy-front-end-v1.yml_"](https://github.com/Peralta-CashFlow/CashFlow-WorkFlows/blob/main/.github/workflows/deploy-front-end-v1.yml) is responsible for building and deploying React code on [GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages). It will automatically update the branch "_deploy_" that will be linked with GitHub pages.

## Deploy Java Packages

The workflow ["_java-packages-deploy-v1.yml_"](https://github.com/Peralta-CashFlow/CashFlow-WorkFlows/blob/main/.github/workflows/java-packages-deploy-v1.yml) is responsible for automatically deploying Java packages to [Github Packages](https://docs.github.com/en/packages/learn-github-packages/introduction-to-github-packages).

# Configuring a new Java Project

For Back-End **Java** repositories we will configure Sonar and Conventional Commits validation on Pull Requests. 

### Let's do it:

1 - Create the new Repository;

2 - Request Architecture team to configure the repository on [SonarCloud](https://sonarcloud.io/organizations/peralta-cashflow/);

2 - Add on the project pom.xml under properties the bellow configuration:

```xml
<properties>
	<java.version>21</java.version>
	<sonar.organization>peralta-cashflow</sonar.organization>
	<sonar.host.url>https://sonarcloud.io</sonar.host.url>
	<sonar.qualitygate.wait>true</sonar.qualitygate.wait>
</properties>
```

3 - Add ["_pull-request-back-end-v1_"](https://github.com/Peralta-CashFlow/CashFlow-WorkFlows/blob/main/.github/workflows/pull-request-back-end-v1.yml) on the project .github/workflows folder:

```yml
name: Pull Request - Java Back End

on:
  pull_request:
    types: [opened, synchronize, reopened]
    branches:
      - main

jobs:
    back-end-workflow:
        name: Call Back-End Pull Request Workflow
        uses: Peralta-CashFlow/CashFlow-WorkFlows/.github/workflows/pull-request-back-end-v1.yml@main
        permissions:
            contents: read
            pull-requests: write 
        with:
            sonar_project_key: <PROJECT_KEY_FROM_SONAR_CLOUD>
```

4 - Request Architecture team to protect main branch;

# Configuring a new React Project

For Front-End **React** repositories we will configure Sonar, Conventional Commits validation on Pull Requests and Deploy to GitHub Pages.

1 - Create the new Repository;

2 - Request Architecture team to configure the repository on [SonarCloud](https://sonarcloud.io/organizations/peralta-cashflow/) (for React repository we will use Sonar automatic integration);

3 - Add on your project src the "_sonar-project.properties_" file:

```properties
sonar.qualitygate.wait=true
```

4 - Add ["_pull-request-front-end-v1_"](https://github.com/Peralta-CashFlow/CashFlow-WorkFlows/blob/main/.github/workflows/pull-request-front-end-v1.yml) on the project .github/workflows folder:

```yml
name: Pull Request - React Front End

on:
  pull_request:
    types: [opened, synchronize, reopened]
    branches:
      - main

jobs:
    front-end-pull-request-workflow:
        name: Call Front-End Pull Request Workflow
        uses: Peralta-CashFlow/CashFlow-WorkFlows/.github/workflows/pull-request-front-end-v1.yml@main
        permissions:
            contents: read
            pull-requests: write 
```

5 - Request Architecture team to create protected environment "_github-pages_";

6 - Add ["_deploy-front-end-v1_"](https://github.com/Peralta-CashFlow/CashFlow-WorkFlows/blob/main/.github/workflows/deploy-front-end-v1.yml) on the project .github/workflows folder:

```yml
name: Deploy - React to GitHub Pages

on: 
   push:
    branches:
        - main

jobs:
    front-end-deploy-workflow:
        name: Call Front-End Deploy Workflow
        uses: Peralta-CashFlow/CashFlow-WorkFlows/.github/workflows/deploy-front-end-v1.yml@main
        environment: github-pages
        permissions:
            contents: write
```

7 - Request Architecture team to protect main branch and initialize GitHub pages on "_deploy_" branch;
