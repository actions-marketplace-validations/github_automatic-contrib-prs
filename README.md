# automatic-contrib-prs
Automatically open a pull request for repositories that have no `CONTRIBUTING.md` file for a targeted set of repositories.

## What this repository does
This code is for a GitHub Action that opens pull requests in the repositories that have a specified repository topic and also don't have a `CONTRIBUTING.md` file.

## Why would someone do this
It is desirable, for example, for all Open Source and InnerSource projects to have a `CONTRIBUTING.md` file that specifies for new contributors what the processes and procedures are for making a new contribution.

## How it does this
- It pulls a list of labelled repositories from a `repos.json` which can be generated by the [InnerSource-Crawler GitHub Action](https://github.com/marketplace/actions/innersource-crawler).
- It opens a pull request in each of those repositories which adds the `CONTRIBUTING.md` file with some template contents.

## Use as a GitHub Action

1. Create a repository to host this GitHub Action or select an existing repository.
1. Create the env values from the sample workflow below (GH_TOKEN, GH_ACTOR, PR_TITLE, PR_BODY, and ORGANIZATION) with your information as repository secrets. More info on creating secrets can be found [here](https://docs.github.com/en/actions/security-guides/encrypted-secrets).
Note: Your GitHub token will need to have read/write access to all the repositories in the `repos.json` file.
1. Copy the below example workflow to your repository and put it in the `.github/workflows/` directory with the file extension `.yml` (ie. `.github/workflows/auto-contrib-file.yml`)

### Example workflow
```yaml
name: Find proper repos and open CONTRIBUTING.md prs

on:
  workflow_dispatch:

jobs:
  build:
    name: Open CONTRIBUTING.md in OSS if it doesnt exist
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2
    
    - name: Find OSS repository in organization
      uses: docker://ghcr.io/zkoppert/innersource-crawler:v1
      env:
        GH_TOKEN: ${{ secrets.GH_TOKEN }}
        ORGANIZATION: ${{ secrets.ORGANIZATION }}
        TOPIC: open-source

    - name: Open pull requests in OSS repository that are missing contrib files
      uses: docker://ghcr.io/github/automatic-contrib-prs:v1
      env:
        GH_TOKEN: ${{ secrets.GH_TOKEN }}
        ORGANIZATION: ${{ secrets.ORGANIZATION }}
        GH_ACTOR: ${{ secrets.GH_ACTOR }}
        PR_TITLE: ${{ secrets.PR_TITLE }}
        PR_BODY: ${{ secrets.PR_BODY }}
```

## Instructions to run locally without Docker
- Clone the repository or open a codespace
- Create a personal access token with read only permissions
- Copy the `.env-example` file to `.env`
- Edit the `.env` file by adding your Personal Access Token to it and the desired organization, pull request title and body, and actor (GitHub username)
- Install dependencies `python3 -m pip install -r requirements.txt`
- Run the code `python3 open_contrib_pr.py`
- After running locally this will have changed your git config user.name and user.email so those should be reset for this repository
