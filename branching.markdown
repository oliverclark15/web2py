# Table of Contents
- [Branching Convention](#branching-convention)
  * [Primary Branches](#primary-branches)
    + [Master](#master)
    + [Development](#development)
  * [Supporting Branches](#supporting-branches)
    + [Features](#features)
    + [Releases](#releases)
      - [Branch Creation](#branch-creation)
      - [Branch Finalization](#branch-finalization)
    + [Hotfixes](#hotfixes)
      - [Branch Creation](#branch-creation-1)
      - [Branch Finalization](#branch-finalization-1)
    + [Workflow Diagram](#workflow-diagram)
   
# Branching Convention

The branching conventions used in this project are based on Vincent Driessen's  [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/). 

|     Type    | Branch Naming Convention | Branches From |     Merges Into     |
|:-----------:|:-------------------------:|:--------------:|:--------------------:|
|    Master   |           `master`          |        -       |           -          |
| Development |            `dev`           |        -       |           -          |
|   Release   |          `R-x.x.x`          |   Development  | Development,  Master |
|   Feature   |    `feature/feature-name`   |   Development  |      Development     |
|    Hotfix   |     `hotfix/hotfix-name`    |     Master     |  Development, Master |

## Primary Branches
This branching model uses two primary branches; `master` and `dev` who exist throughout the projects lifespan.
### Master

`master` contains the latest development updates and always holds a production ready version of the source code. `master` accepts changes from feature branches.

### Development

`stable` points to the most recently **deployed** version. Upon the `master` branch being deemed stable and ready to be deployed, changes from `master` are merged into `stable.`

## Supporting Branches
Each supporting branch has a unique function and each supporting branch adheres to strict rules regarding from which branch they originate as well as which branches can be merge targets.
### Features
Feature branches are used to develop future functionality for future releases. The following steps outline the expected developer workflow to add a new future for future release.

 1.  Developer creates a new branch based from  `dev`  branch.
```console
$ git checkout -b feature/my-new-feature dev
Switched to a new branch "feature/my-new-feature"
```
 2. Developer implements new feature.
 3. Assuming the developer wants to incorporate the new feature into future releases, the new feature branch can be merged back into the `dev` branch.
 ``` console
$ git checkout dev
Switched to branch 'dev'
$ git merge feature/my-new-feature
Updating ab9c24e..12f1823
$ git branch -d feature/my-new-feature
Deleted branch feature/my-new-feature (was 12f1823).
$ git push origin dev
```

### Releases
Release branches allow preparation of the latest production release. When features are ready (in `dev`) and the team is ready for a release the following procedure is followed.
#### Branch Creation
A new release branch is based from the `dev` branch. If we are ready for the next release 
(say 2.1.3).
``` console
$ git checkout -b R-2.1.3 dev
switched to a new branch "R-2.1.3"
...
... make necessary admin changes (version metadata, build info, etc)
...
$ git commit -a -m "Bumped version number to 2.1.3"
[R-2.1.3 38d1238] Bumped version number to 2.1.3
1 files changed, 1 insertions(+), 1 deletions(-)
```
At this point the R-2.1.3 branch is not a *real* release. However, it is ready to be incorporated into the master branch (which makes it a *real* release).

#### Branch Finalization
``` console
$ git checkout master
Switched to branch 'master'
$ git merge --no-ff R-2.1.3
Merge made by recursive.
$ git tag -a R-2.1.3
```
This makes the R-2.1.3 branch an official release by merging into master and tagging the commit accordingly.
Next, the development branch must be updated to include the changes of this release.
``` console
$ git checkout dev
Switched to branch 'dev'
$ git merge --no-ff R-2.1.3
Merge made by recursive.
```
Finally, we can remove the release branch as it has been fully incorporated into both the primary branches (`master`	and `dev`).
``` console
$ git branch -d R-2.1.3
Deleted branch R-2.1.3 (was fd452aa).
```

### Hotfixes

Hotfix branches are similar to release branches. However, they come when the team needs to act quickly upon a bug/issue in production release. Hotfix branches are based on `master` branch while feature branches are based on `dev` branch When a release has an issue, a hotfix branch is made from the corresponding tag on master branch (represents production release). The following procedure is followed to make a live hotfix.
#### Branch Creation
Consider the case when release 2.1.3 (R-2.1.3) is found to be buggy and a hotfix is required.
``` console
$ git checkout -b hotfix/2.1.4 master
Switched to a new branch "hotfix/2.1.4"
...
... make necessary admin changes (version metadata, build info, etc)
...
$ git commit -a -m "Bumped version number to 2.1.4"
[hotfix/2.1.4 32f51ac] Bumped version number to 2.1.4
```
Next, depending on issues with release, fix those issues and commit

``` console
$ git commit -m "Fixed severe production problem"
[hotfix/2.1.4 acdf3a6] Fixed severe production problem
5 files changed, 22 insertions(+), 11 deletions(-)
````

#### Branch Finalization
To finish our hotfix, we need to incorporate our changes into `master` and `dev`.
This is done by first updating `master` and tagging the new release.
``` console
$ git checkout master
Switched to branch 'master'
$ git merge --no-ff hotfix/2.1.4
Merge made by recursive.
$ git tag -a 2.1.4
```
Before incorporating the changes to `dev`

``` console
$ git checkout dev
Switched to branch 'dev'
$ git merge --no-ff hotfix/2.1.4
Merge made by recursive.
```
Finally, delete the hotfix branch
``` console
$ git branch -d hotfix/2.1.4
Deleted branch hotfix/2.1.4 (was acdf3a6).
```
### Workflow Diagram
![Branch Model](https://github.com/oliverclark15/web2py/blob/master/Git-branching-model.png)


