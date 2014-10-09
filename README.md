pipeline cookbook
=================
Continuous delivery pipeline for Chef.

### Overview

As you commit Chef artifacts to git (i.e. cookbooks, data bags, environments and roles) the pipeline will test and load them into Chef automatically.

The pipeline is Jenkins based and relies on a git repository of your Chef items of the following structure:

    chef_repo/Berksfile
    chef_repo/environments/
    chef_repo/roles/
    chef_repo/data_bags/


#### Description

- **Berksfile:** 
  - Contains all the cookbooks in your project.
  - Used to generate cookbook jobs for cookbooks that need testing
  - Used to upload community cookbooks that don't need testing
- **environments/:** Directory of environment files in a format accepted by `knife upload`
- **roles/:** Directory of role files in a format accepted by `knife upload`
- **data_bags_/:** Directory of data bag files in a format accepted by `knife upload`

### Jobs

Both of the following job types listen on their respective repositories and upload to Chef when changes are committed to git.

Once the pipeline is up and fully flushed out you will have N+1 cookbooks in your Jenkins dashboard, where N is the number of cookbooks you have that require testing.  And the +1 is the chef\_repo job which is responsible for uploading all the roles, environments and data bags as well as cookbooks that bypass testing.

#### Chef Repo Job
- Uploads roles, environments, data bags and community cookbooks from the git repo to the Chef server

#### Cookbook Jobs
- Lint and test the respective cookbook
- Upload respective cookbook from git to the Chef server

## Instructions

Note that there is a dependency between the chef-client and a Jenkins job.  The cookbook jobs are generated by the chef-client from the chef\_repo that is kept up to date by Jenkins.  Because of this the chef-client will need to run twice on initial set up of the pipeline server to flush out all of the cookbook jobs.  

### Try it!
From the command line, run the following twice:

`kitchen converge cent`

Now check out your local CD pipeline demo on localhost:8080


### Production Notes

Update the following to a chef\_repo of your own:

`default['pipeline']['chef-repo']['url'] = "https://github.com/stephenlauck/pipeline_chef.git"`


- There is a .chef folder in the jenkins user's home. You will need to put an appropriate .pem file in there for a pipeline user with admin rights to Chef.
- This uses the Chef embedded version of Ruby for knife and Berkshelf etc.
- Since this uses Berkshelf for cookbook uploads, all cookbook versions are frozen by default.  Failure to update the metadata version number should result in a failed job.
- Since this uses Berkshelf to upload your cookbooks to Chef, there is an expectation that every cookbook to be tested contain the usual Berkshelf workflow tools such as kitchen, vagrant and berks.


### Platform

* Ubuntu 12.04
* CentOS 6.5



Authors
-----------------
- Author: Stephen Lauck (<lauck@getchef.com>)
- Author: Mauricio Silva (<mauricio.silva@gmail.com>)
- Contributor: Kennon Kwok <kennon@getchef.com>
- Contributor: Kirt Fitzpatrick <kfitzpatrick@getchef.com>
