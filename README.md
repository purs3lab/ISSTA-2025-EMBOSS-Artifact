# #481 Finding 709 Defects in 258 Projects: An Experience Report on Applying CodeQL to Open-Source Embedded Software (Experience Paper)
This artifact contains the GitHub workflows to run CodeQL on EMBOSS repositories in our dataset, the results of running CodeQL on these repositories, and our manual analysis of CodeQL results.

## Getting started
### Directory structure of artifact
```
.
├── issta2025-paper481.pdf  # Preprint of the paper
├── OSSEmbeddedResults      # CodeQL results on EMBOSS repos
├── README.md
├── runner                  # Docker image to set up a self-hosted runner
│   ├── codeql-home
│   │   └── codeql-repo     # Our customized CodeQL rules
│   └── Dockerfile
├── scanner-workflows       # Workflows to run CodeQL on EMBOSS repos
└── SurveyReport.pdf        # Raw survery results
```
### Running CodeQL workflows
scanner-workflows/.github/ contains GitHub workflows to run CodeQL on repositories in our dataset. To run these workflows, follow the steps here.

#### Requirements
A GitHub account and a x64 Linux machine with docker installed.

#### Steps to create a GitHub repository and set up a self-hosted runner
1. Create a new GitHub repository named scanner-workflows and copy the directory scanner-workflows/.github/ to the created repo.
2. Add a self-hosted runner to the created repo by following [instructions](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/adding-self-hosted-runners). Choose Linux as the operating system image and x64 as architecture.
You will see the commands to configure a runner. DO NOT RUN THESE COMMANDS.
Find a configuration command that starts with `./config.sh` and contains a token (e.g. `./config.sh --url https://github.com/OWNER/scanner-workflows --token AABBCCDD112233`). Save the token `AABBCCDD112233`.
3. Change working directory to runner/ of THIS ARTIFACT (not the scanner-workflows repo you just created) and then build and run a docker container
``` bash
cd runner/
docker build -t codeql-gh-runner .
docker run -it --rm codeql-gh-runner
```
Then inside the container:
``` bash
./config.sh --unattended --url https://github.com/OWNER/scanner-workflows --token RUNNER_TOKEN
./run.sh
```
Remember to replace OWNER with your GitHub username and RUNNER_TOKEN with the token you saved in step2.

#### Running a workflow
1. On GitHub, navigate to the main page of the scanner-workflows repository you created.
2. Under your repository name, click **Actions**.
3. In the left sidebar, click the name of the workflow you want to run. For example, you want to run the CodeQL workflow for aws/aws-iot-device-sdk-embedded-C. Then click `CodeQL: aws/aws-iot-device-sdk-embedded-C`.
4. Above the list of workflow runs, click the **Run workflow** button.
5. Select the **Branch** dropdown menu and click the default branch.
6. Fill in the input of the workflow. For example, if you want to scan the commit `929a86dbbd9bb546872b383e955f8b420448c3e9` of `aws/aws-iot-device-sdk-embedded-C`, then
  - `Repository to run CodeQL on. Format: owner/repo.`: Keep the default value, i.e., `aws/aws-iot-device-sdk-embedded-C`.
  - `The branch, tag or SHA to checkout. Default: default branch.`: Enter `929a86dbbd9bb546872b383e955f8b420448c3e9`.
7. Click **Run workflow**.
8. The CodeQL result (a SARIF file) will be in the artifact of the workflow run. It should be the same as `OSSEmbeddedResults/aws/aws-iot-device-sdk-embedded-C/d8c63c0df2bd2252727ec626f4971f61b147add5/cpp.sarif` in this artifact.

Refer to the GitHub official [doc](https://docs.github.com/en/actions/managing-workflow-runs-and-deployments/managing-workflow-runs/manually-running-a-workflow?tool=webui) for more information on manually running a workflow.



### CodeQL results
OSSEmbeddedResults/ contains the CodeQL running results (SARIF files) for all repos in our dataset.
You can view them via the SARIF viewer extension of VSCode.

## Detailed description
Details of results of the paper are shown in this [this Google Spread Sheet](https://docs.google.com/spreadsheets/d/11x4Jx2jIqCrq_V7TPbyGm86Zq4OQ-_MvqQbSjBWO5tA/edit?usp=sharing). Here is the explanation of all sheets in it:

* *Repos from GitHub*. The list of EMBOSS repos we retrived from GitHub. (Figure 1 in papar.)
* *Repos from osrtos*. The list of RTOS repos we retrived from osrtos.com. (Figure 1 in papar.)
* *Responsible Disclosure*. Our manual analysis of CodeQL results, including the defects we found in Open-Source Embedded Software and the links of pull requests for patching these defects. (Table 4 in paper.)
* *PRs of CodeQL workflows*. Pull requests of adding CodeQL workflows in EMBOSS repos (Table 4 in paper.)
* *False positives*. Our manual ananlysis of precision of CodeQL. (Figure 8 in paper.)
* *Embedded Criticality Scores*. Criticality scores of EMBOSS. (Section 2.1.2 in paper.)


`SurveyReport.pdf` gives the raw survey results. (Figure 2 in paper.)