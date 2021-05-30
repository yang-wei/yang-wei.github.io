+++
title = "Git Slash Github Flow for Beginner and Small Team"
date = "2014-09-16"
slug = "2014/09/16/git-slash-github-flow-for-beginner-and-small-team"
categories = ["Git"]
description = "git work flow for beginner and small team using github"
+++

It's been a while since my last update. I am back and would like to write down something I learned through both internships which I participated last month.
One of the new thing I want to highlight is probably *team developement flow on github*. Before this, I was using github in my personal project like [simplified](https://github.com/yang-wei/simplified) and everything was easy. No issue, no pull request and no conflict !!! <!-- more --> However, thing totally changes when there are multiple developers in the same project. Of course we can just use the successful Git branching model - [gitflow](http://nvie.com/posts/a-successful-git-branching-model/) in our development, but this seems too complicated for git beginner and small team(3-5 peoples). So we simplified this workflow to suit our project and it turned out quite successful. In this blog post, I will write down our __Git + Github workflow__ during our development.


##The main steps
 + Create new issue
 + Update local master branch
 + New branch for the issue
 + Write awesome code (commit)
 + Push to the remote repository
 + Create pull request

Among all of these steps, only the first and last step are done on remote repository(github). Let's look at them one by one.

###Create new issue
On github page repository, go to the `Issues` page and open a new issue. Here issue will be created and labeled by something like *#number*. That will be our issue number.
Issue name should be clear and semantic. In our development it looks like this.
```
CSRF Basic Prevention #186
// #186 is our issus number
```
Issue number is important during development on github. It links back to issue page from your commit message or pull request. To make our development history clean and clear, everytime before we start adding function or fix bug, new issue will be opened and discussed among team members. When the new issue is approved by other members, it will be assigned to a team member and he/she will proceed to the next step. 

###Update local master branch
After issue is assigned, we will start working on our local repository. Everytime we want to make sure our local master is up-to-date.
```
$ git pull origin master
```

###New branch for the issue
After confirming we have the latest master, we can create a new branch to add new feature.
```
$ git checkout -b iss186-csrf-defend
```
Our branch name looks like `iss<issue number>-<feature name>`. This makes thing easier when we deploy our branch to github.

###Write awesome code
Here we start writing code for our new feature. Staging and commiting are done frequently in this step. When we think we are done, our final staging and commiting will look like
```
$ git add .
$ git commit -m "CSRF Prevention by One Time Token close #186"
```
Note that the `close #186` behind the commit message. By adding [special keyword syntax](https://help.github.com/articles/closing-issues-via-commit-messages) in our commit message,
issue we created will be automatically closed when this branch is merge successfully. Of course, other special keyword syntax like `resolve #186`, `fix #186` work as well. I really think github is awesome when I know this feature.

###Push to the remote repository
Nothing difficult here but to make sure no conflict on the remote repository, it's good to update current branch to the latest.
```
git pull origin master
git push origin iss186-csrf-defend
```
Where `iss186-csrf-defend` is our current branch.
#####Pro Tip
We can also avoid retyping our branch name by doing so.
```
git push origin HEAD
```
Where `HEAD` will be point to our current branch.

###Create pull request
Awesome, our code are on github. It's time to let other member know that our feature are ready and confirmation is needed. On github repository, create a pull request, compare master with our newly pushed branch(in this case `iss186-csrf-prevention`). Other member will be alert and review the code. Discussion can be done here among the members. When review is done and we received  [LGTM](http://www.lgtm.in/) message, someone(other team member) will merge the pull request. Issue will be closed automatically if commit messages contain special keyword syntax. First step will be repeated.

It seems like a long step to repeat these flow everytime during new feature, or even minor typo fix. Yes it is ! However, during team development it's important to keep track of every feature even it's small as typo. By tracking all these records, project maintainance in the future will be easier. Also it's important to discuss team-style-workflow `eg: branch naming, commit message style` before every development start.

{% blockquote Roald Amundsen %}
Adventure is just bad planing.
{% endblockquote %}

These workflow are just a workflow of my team during internship. If you just start your team development by using git + github, I hope this can be helpful. For a bigger and advanced project, [gitflow](http://nvie.com/posts/a-successful-git-branching-model/) will be cool.

