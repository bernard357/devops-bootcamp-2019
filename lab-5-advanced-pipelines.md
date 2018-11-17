# Hands-on with Service Catalog, CodePipeline, CodeBuild

credit: “Datalake data transformation orchestration on AWS” from Paul de Monchy

## Instructions

Over next 20 minutes, you are tasked to visit following page and create a datalake
batch pipeline with Service Catalog: https://bit.ly/2FoEh2R

If you have extra time, improve the code base and submit a pull request to Paul
over GitHub. Thank you!

## Additional notes

### Step 1 - Create Service Catalog products

From the Service catalog console, select the Products list from the Admin
panel in the left menu.

Create a first product with following attributes:
* Product name: CloudTrail for S3 Datalakes
* Product details: Create a CloudTrail on S3 arns that are to be followed for event triggering.
* Provided by: lePaulo

Click on Next and then on Next again.

In the panel, click on 'Specify a URL location for an Amazon CloudFormation template'
and paste: https://bit.ly/2PzvLTd

For the version, you can type 'June 2018'.

Create a second product with following attributes:
* Product name: Datalake Batch pipeline
* Product details: Create a CodePipeline CI/CD pipeline for automatic deployment of project modifications on commit.
* Provided by: lePaulo

Click on Next and then on Next again.

In the panel, click on 'Specify a URL location for an Amazon CloudFormation template'
and paste: https://bit.ly/2Ds1ApL

For the version, you can type 'June 2018'.

Then Create a portfolio for your products, using your GitHub identifier as a prefix.
Last but not least, share the portfolio with yourself so that you can consume products.

### Step 2 - Provision a Git repository

Go to the CodeCommit console, and create a new repository 'datalake-pipeline'.

For git commands to work with CodeCommit you have the choice of HTTPS or SSH access.
The setup of SSH only requires that you upload your public key to your IAM user so it
may be a bit quicker. Look at files in `~/.ssh` on your workstation and display the content
of a public key (it ends with `.pub`). Multiple solutions exist for the creation of a new
SSH key, for example:

```shell
$ ssh-keygen -t rsa
```

Then go to the IAM console, select your user profile,
and in the tab 'Security credentials', click on 'Upload SSH public key'.
Note the SSH Key ID since it will be used on next step.

The bridge between your AWS identity and the SSH key is done in the
file `~/.ssh/config` on your workstation. For example:

```shell
$ nano ~/.ssh/config
```

Content should be similar to the following:

```
Host git-codecommit.*.amazonaws.com
    User <Your-AWS-SSHs-Key-ID>
    IdentityFile ~/.ssh/<name-of-private-key-without-the-.pub-extension>
```

Now we can clone the repository locally, and populate it from the public
repository that has been shared by Paul de Monchy:

```shell
$ git clone ssh://git-codecommit.eu-west-1.amazonaws.com/v1/repos/datalake-pipeline
$ git clone --depth 1 https://github.com/lePaulo/AWSDatalakeDataTransformationOrchestration.git
$ mv AWSDatalakeDataTransformationOrchestration/* datalake-pipeline/
$ rm -rf AWSDatalakeDataTransformationOrchestration/
$ cd datalake-pipeline/
$ git add .
$ git commit -m 'initial push'
$ git push
```

### Step 3 - Create your datalake

In the same region where you grow the Service Catalog, create a bucket
with your GitHub identifier as a prefix and with the suffix '-data-artifacts'.
Take a note of the ARN of the new S3 bucket.

### Step 4 - Provision a Cloudtrail for S3 Datalakes

From the Service Catalog console, check the Products list at the top of the left menu.
Pick up `CloudTrail for S3 Datalakes` and hit 'Launch product'.
Provide with a name, for example 'myTrail', select the version, and hit 'NEXT'.

Paste a comma-separated list of S3 ARN and prefixes, for example:
`arn:aws:s3:::bernard357-datalake-artifacts/raw/,arn:aws:s3:::bernard357-datalake-artifacts/refined/`

Hit 'NEXT'. The creation of this product will trigger CloudFormation, so you
can troubleshoot there in case of any problem.

Ensure that the product is successfully listed in the 'Service Catalog' console,
under the 'Provisioned products list' menu.

### Step 5 - Customize the parameter file

Create a small VPC in the same region by providing a name, aka, 'myVPC', and the template at https://s3.amazonaws.com/codebuild-cloudformation-templates-public/vpc_cloudformation_template.yml

When deployment has been completed, after 4 or 5 minutes, look at the Outputs tab.
Take note of values for keys VPC and PublicSubnets.

Reflect these attributes in the local configuration file:

```shell
$ nano AWS_CICD/config.json
```

Hit Ctrl-O to save content, and Ctrl-X to exit. Then push everything
to the git repository:

```shell
$ git commit -am 'set actual VPC configuration'
$ git push
```

### Step 6 - Provision a CI/CD pipeline

From the Service Catalog console, check the Products list at the top of the left menu.
Pick up `Datalake Batch pipeline` and hit 'Launch product'.
Provide with a name, for example 'myPipeline', select the version, and hit 'NEXT'.

On the following page you have to provide several attributes:

* Application name: myapplication

* Git repository used: <name-of-repository-created-on-step-2>

* Datalake Bucket Name: <name-of-bucket-created-on-step-3>

Let other attributes unchanged and hit Next, then complete the product launch.

### Test the data transformation orchestration product

Visit the CodePipeline console so as to wait for the previous deployment pipeline to complete.
Now we can upload a new file and see how a Step Function will handle it automatically:

```shell
$ echo "hello world" >fileA.csv
$ aws s3 cp fileA.csv s3://<name-of-bucket-created-on-step-3>/raw/fileA.csv
```

Visit the Step Functions console and select the 'DataPrepStateMachine' state machine.
Wait for it to feature some successful execution, that you can inspect manually.

Congratulations! You can now visit the Service Catalog console and terminate all
products so as to save on cloud costs.
