# Hands-on with CloudFormation and CodePipeline

credit: “Use AWS TaskCat, AWS CodePipeline, and AWS CodeBuild to test and deploy your templates” from AWS Quick Starts

## Instructions

Over next 20 minutes, you are tasked to:

* Secure GitHub credentials - go to https://www.github.com and create an account for yourself if needed

* Go to following page and setup a new pipeline to test your templates with TaskCat: https://amzn.to/2B98myY

If you have extra time: introduce some faults to a CloudFormation template, commit, push, and show evidence of TaskCat feedback

## Additional notes

1) For this lab you will need a GitHub account. If you do not have one yet, then take 5 minutes to create
one at https://github.com/ before everythiong else.

2) Generate a OAuth2 token from https://github.com/settings/tokens and add 'repo' and 'admin:repo_hook'
permissions. You will have to paste it in the CloudFormation web form.

3) On the CloudFormation Details page you can use following settings for GitHub Configuration:

* Repository owner: <your-github-identifier>

* Repository name: iac-with-taskcat

* Source branch: master

* Release branch: release

* OAuth2 token: <your-oauth2-token>

4) It will too 5 to 8 minutes for the creation of full stack. In the meantime you can read the on-line
documentation starting at https://docs.aws.amazon.com/quickstart/latest/cicd-taskcat/overview.html

5) You may also take some time to review the overall template in the CloudFormation Designer.
Select YAML so as to visualize items and their attributes. Click on various items so as
to inspect them.

6) Create a repository on GitHub at https://github.com/new so as to store templates.
Provide the name that you gave to CloudFormation.

7) For this lab you will not have to create your own templates. We will copy
sample files provided by the TaskCat team instead:

    $ git clone https://github.com/<your-github-identifier>/iac-with-taskcat.git
    $ cd iac-with-taskcat/
    $ git clone --depth 1 https://github.com/aws-quickstart/taskcat.git
    $ ls taskcat/examples/sample-taskcat-project/
    ci		scripts		templates
    $ mv taskcat/examples/sample-taskcat-project/* .
    $ rm -rf taskcat/
    $ ls
    ci		scripts		templates
    $ git status
    $ git add .
    $ git status
    $ git commit -m 'initial push using TaskCat sample files'

We then create a branch for future releases of templates, and then push
everything to GitHub:

    $ git checkout -b release
    $ git push --set-upstream origin release
    $ git checkout master
    $ git push

8) Visit the GitHub page of your project to ensure that files have been pushed there.
Also ensure that there are two branches in your project: "master" and "release"

9) Visit the CloudFormation page and select the Outputs tab. From there, pick up
the CodePipelineURL text and paste it in a browser. This will allow you to
inspect the work done by the pipeline on your templates. It should be a FAILURE.

10) Edit the configuration file on your workstation:

    $ nano ci/taskcat.yml

Ensure that qsname is set with the name of your repository, aka, iac-with-taskcat and that
you keep only 2 or 3 regions for deployment.

Save with Ctrl-O and exit with Ctrl-X

    $ nano ci/debug-input.json

Change settings related to LocalOverrideTests (from "override" to "hello") and
to GlobalOverrideTests (from "override" to "world") since the pipeline prevents
the value "override" there. Save with Ctrl-O and exit with Ctrl-X

Push your changes to as to relaunch the pipeline:

    $ git diff
    $ git add .
    $ git commit -m 'fix the TaskCat configuration'
    $ git push

11) Go back to the CodePipeline page and watch the work done there. It should be
a SUCCESS.

12) If you have time, inspect the last report from the S3 bucket.
