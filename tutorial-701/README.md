# Deploying to on-demand environments

This tutorial demonstrates how to automatically deploy feature
branches and (if you use GitHub) pull requests to Acquia Cloud On
Demand Environments (ODEs) using the Pipelines Deploy tool.

# Acquia Cloud CD beta has ended
The Acquia Cloud CD which integrates Pipelines with CD environments has ended. The pipelines beta continues. For customers who would like to use CDEs please reach out to your account manager to request purchasing CDEs.

# How it works
The Pipelines Deploy tool provides integration between Pipelines and
Cloud environments. When the Deploy tool runs during a "build" event:

* The Deploy tool checks to see if a Cloud environment already exists
  for the build branch, such as pipelines-build-feature (for a feature
  branch) or pipelines-build-pr-N (for GitHub pull request #N), that
  will hold the build artifact resulting from the current job.
* If no such Cloud environment exists, the Deploy tool creates one
  using the Cloud On Demand Environments feature and configured it to
  deploy the build branch. This requires that you have added this
  feature to your Acquia Cloud subscription.
* If such a Cloud environment already exists, it is left in place.
* Acquia Cloud will then deploy the build branch to the selected
  environment.

When the Deploy tool runs during a "pr-merged" event, which is triggered
when a GitHub pull request is merged to its base branch:

* The Deploy tool deletes any Cloud on-demand environments deploying
  the build branch, pipelines-build-pr-N (for GitHub pull request #N),
  for that pull request.

When the Deploy tool runs during a "pr-closed" event, which is triggered
when a GitHub pull request is closed without being merged to its base branch:

* The Deploy tool deletes any Cloud on-demand environments deploying
  the build branch, pipelines-build-pr-N (for GitHub pull request #N),
  for that pull request.

The net result of these behaviors by the Deploy tool is that every
feature branch and pull request will get its own on-demand environment
that is updated for every build of that branch performed by Pipelines,
and on-demand environments for pull requests are deleted automatically
when the pull request is merged.

# How to use it
To use this tutorial, the steps are:

* Create a feature branch in your Git repo.  Since this tutorial demonstrates Pipelines deployment, call the branch pipelines-deploy:
```
  git checkout master
  git checkout -b pipelines-deploy
```
* Copy the `acquia-pipelines.yaml` file from this tutorial into your Git repo (now on the pipelines-deploy branch). You can:
  * cut and paste the [contents of the file](https://raw.githubusercontent.com/acquia/pipelines-examples/master/tutorial-701/acquia-pipelines.yaml),

<b>OR</b>

  * clone this repository and look in the tutorial-701 folder.
* Commit `acquia-pipelines.yaml` to the repo and push it:
```
  git add acquia-pipelines.yaml
  git commit -m 'Add Pipelines YAML file'
  git push origin pipelines-deploy
```
* If you [use GitHub with Acquia Pipelines](https://docs.acquia.com/acquia-cloud/develop/pipelines/connect/github/), a job will start immediately.  Otherwise, you can run ```pipelines start``` or click the Start Job button from the Pipelines UI.
* When ```pipelines status``` shows the job is complete, visit your site on Acquia Cloud. A new environment named 'pipelines-build-pipelines-deploy' will exist, running the build of the pipelines-deploy branch.
* Merge the pipelines-deploy branch into master:
```
  git checkout master
  git merge pipelines-deploy
  git branch -d pipelines-deploy
```
* Now, all of your new feature branches and pull requests will get their own Cloud environment.
