# Push-to-ECR-Repo-in-Another-AWS-Account

This repository creates a simple build project to build and push docker images to an ECR private repository in another AWS account. 

AWS Resources to use:

IAM
CODECOMMIT
CODEBUILD
ECR

AWS ACCOUNT A:
Have the codecommit and codebuild in account A.

On the CodeCommit Repository add the Dockerfile and the buildspec.yml file to build the docker image and push to the ECR. Have attached samples on the code section.

Create a build project with source as the CodeCommit Repo.

Ensure the build project role has the below ECR permissions:

==================================
{
    "Version": "2008-10-17",
    "Statement": [
        {
            "Sid": "AllowCrossAccountPush",
            "Effect": "Allow",
            "Action": [
                "ecr:*"
            ],
            "Resource": "*"
        }
    ]
}
===============================

Create a pipeline with CodePipeline with source stage as the CodeCommit Repo and build stage as the build project created above. 

AWS ACCOUNT B:
Have the private ECR Repository in Account B

Edit the permissions of the ECR repository by adding the below JSON Policy:
============================================
{
  "Version": "2008-10-17",
  "Statement": [
    {
      "Sid": "AllowCrossAccountPush",
      "Effect": "Allow",
      "Principal": {
        "Service": "codebuild.amazonaws.com",
        "AWS": [
          "arn:aws:iam::ACCOUNT-A-ID:root",
          "arn:aws:iam::ACCOUNT-A-ID:role/service-role/codebuild-test-ecr-cc-service-role" #the codebuild build project role.
        ]
      },
      "Action": "ecr:*"
    }
  ]
}
===============================================

