# App Development using GitHub


# Table of content

---

# Basic Development Commands (Git command)

- [Git Command Ref.]()
- [Git Workflow]()

---

## Step1. Forking Frappe / ERPNext

 In order to make changes to Frappe and ERPNext, you need to set up a fork on your own GitHub account. This can be done easily on the GitHub website.

- Log in to GitHub

 Go to github.com/frappe/frappe, Click on the Fork button at the top right of the screen.

- Setting up local access to your fork

  When making changes to your local installation, you will need to be able to update the online repository so that you can push changes to Frappe / ERPNext, as well as keep revisions on GitHub, etc.

 A). Access your local installation using a terminal and go to the frappe-bench folder

 *Note:* For each app you have forked, use the following commands to establish a remote to your fork:

 ```shell
 ## change director to app (working dir)
 
 cd apps/[app_name]
 
 ## Add remote repo to local repo
 
 git remote add origin https://github.com/[username]/[app_name]     
 ```

 B). Use the following command (in each apps/app_name folder) to verify the remote has been added

 ```shell
 git remote -v # To list all  remote branch repo 
 ```

---

## Step2. Fetch update form remote repo

 To fetch update, `git fetch` is non-destructive command to check update form remote repo to compare with local repo. Using following command:

 ```shell
 # Go to app directory eg. frappe-bench/apps/[app_name]
 cd /frappe-bench/apps/[app_name]

 git fetch [remote repo]
 ```

---

## Step3. Pull Update form Remote

Pulling the latest data from your fork. Typically, using `bench update` will pull the lastest data from the `upstream` (github.com/frappe) server, if pulling frappe or erpnext. If you want to pull your own fork from the repositories, you can use the following command, from the *`apps/[app_name]`* folder.

 ```shell
 # Go to app directory eg. frappe-bench/apps/[app_name]
 cd /frappe-bench/apps/[app_name]

 git pull origin [branch_name]
 ```

 *Recomendation* To pulling update with option `--rebase`. to help developer to manage commit. Git will pull all remote commit to local and then merge local commit on top of the commit of remote repo.
 
 ```shell
 git pull --rebase [remote_repo] [remote_branch]
 ```

---

## Step4. Starting to write your own code.enjoy ur code !!!

 To make a change or create new application, [Here]()

 When you start making changes on applications, whether to frappe, erpnext, and/or your own 
 application, you need to track those changes within a new branch with the following command.

### 4.1 Create a new local branch for development

 To allowed your to create new branch in you local machine and making change or add someting new to this new branch and seperate those change form main branch by this any change in this branch will no effect on main branch.

 ```shell
 #Go to your app/[app_name] folder on your local server (through a terminal)
 git checkout -b [my_branch_name]
 ```

 This allows your to push just these changes to your own github repository(), which will allow you to create pull requests and merge the branch into the frappe/erpnext code, or your own app. The full procedure for starting changes all the way to pushing to your online repository is described below.

### 4.2  Make changes to your app

 This can be done either in the code of your app, or using the web interface of frappe to edit/add doctypes, etc.

 [more about doctype development]()
 
 [more about bench command development]()
 
 [more about website development]()

 When finished making changes, go back to the terminal in your `app/[app_name]` folder (Git repo folder)

---

# git repo Operation

---

## Finding out which files have been modified

 To list all change and new file in Both Untrack files and stage area in local branch using following command 

 ```shell
 git status
 ```

 A list of files, each *highlighted red* will appear. which mean that files in the list are *not* added into the stage aree on local branch repo.

## To include the changes in those files into next commit

 ```shell
 git add [file_name] # Add [file_name] file to stage area 
 ```

 If you want to add all the files that were changed, use:

  ```shell
 git add . # Add all changed files to stage area 
 ```

 A list of files, each *highlighted green* will appear. which mean that files in the list are *Added* into the stage aree on local branch repo.

## Commit the changes to the branch

 This below command, allows your to commit just these changes to your own github repository(local repo), which will allow you to push to your remote repo.

 ```shell
 git commit -am "[message]"
 ```

 where the `[message]` is a short description of the changes you made in this commit

## To Merge new feature to master branch(local master branch)

 If you are done making changes and already commit to local feature branch and wa to branch

 ```shell
 # on Feature branch (local)
 git status # To check working area
 # if working tree is clean, go a head and check difference in change in feature branch with master branch
 git diff <branch_name> <branch_name>
 # go to master branch (target branch to merge into)
 git checkout <target_branch_name> # Eg. git checkout master
 # merge changed branch (feature_branch) to target branch
 git merge [Feature_branch] 
 ```

 [Git Merge Sinario]()

## Push the changes from this commit to your repository

 If you are done making changes to this branch, change back to the `master` or `develop` branch (depending on what were on before)

 ```shell
 git remote -v # to chaeck your remote repo name 
 git push [repo_name] [my_branch_name] #Push to remote branch
 ```

---

# Contibution

## Creating a pull request

 Once you have a branch that has some useful contribution that you want to make part of your main branch, you can create a pull request for that branch and merge the changes.

 Go to your repository through a web browser (https://github.com/[user_name]/[repository])
 Click the New Pull Request button
 Select the base fork to the repository you want to merge into, the base branch to the branch you want to merge into, the head fork to your repository and the compare branch to the branch with your changes.
 Review the changes and make sure that they match the changes you wanted to make.
 If you are happy with the changes, click the create pull request button.
 Bringing your repository in line with the upstream repository
 Because of the high level of development that occurs within Frappe and ERPNext, you will find that very quickly your forked repository goes out of date with the upstream one.

 Go to the `apps/[app_name]` folder in your terminal

 Make sure you are checked out on the branch you want to fix (master or develop typically)

 ```shell
 git checkout [branch_name]
 
 ## Pull the latest data from the upstream repository
 git pull upstream [branch_name]

 ## Rebase your local version to the correct repository/branch
 git rebase upstream/[branch_name]

 ##Check to see if everything went well
 git status

 ##Force your forked repository to use your rebased local data
 git push origin [branch_name] --force
 ```

---

## Testing a branch

 Often times, you might find that you want to try out a branch from another developer by Adding the repository as a remote reposirory

 ```shell
 git remote add [repo_name] http://github.com/[repo_link]
 Pull the latest from the repository

 git pull [repo_name]
 Check out the branch from the repository

 git checkout [branch_name]
 Migrate the bench to take hold of the changes. In the frappe-bench folder:

 bench migrate
 ```

---

## Documentation [Readme.md]

 How to generate documentation for application.

 Tasks to complete from Documentation Refactor

Standardize all pages into: Introduction / How To Create / Features / Related Topics
Update missing Features in all pages
Update screenshots
Add module level flow charts
Add version info in pages and features

### Updating Documentation

 Documentation is a core requirement for any new feature, especially if it is user-facing. Documentation for both ERPNext and Frappe must be maintained separately.

 ERPNext / Frappe documentation can be maintained in both markdown or HTML. See the links below to understand how the documentation is setup.

 Documentation Sources
 [Frappe:](https://github.com/frappe/frappe_docs)
 [ERPNext:](https://github.com/frappe/erpnext_documentation/tree/master/erpnext_documentation/www/docs/user)
 To update the documentation, directly send a pull request on these pages.

### Adding Images
For ERPNext:

You can add images in the /assets/img folder of the documentation app and use it via the path /assets/img/ in your page.

In-app help
ERPNext also offers in-app help, this is also served via the sources for the web documentation. Since both frappe_docs and erpnext_documentation are frappe apps, they can be setup in any bench that wants to serve local documentation.

Adding sidebar
To add sidebar, add a file _sidebar.json in the folder of the page. Example:

[
 {
  "route": "/search_docs", 
  "title": "Search Docs ...", 
  "type": "input"
 }, 
 {
  "route": "/docs", 
  "title": "Docs Home"
 }, 
 {
  "route": "/docs/user", 
  "title": "User Guide"
 }, 
 {
  "route": "/docs/current/api", 
  "title": "Server API"
 }, 
 {
  "route": "/docs/current/models", 
  "title": "Models (Reference)"
 }, 
 {
  "route": "https://github.com/frappe/erpnext/tree/develop/erpnext/docs", 
  "title": "Improve Docs"
 }
]
Prev, Next and Index
If you want to includes links for previous/next or index, add {index}, {prev} or {next} in your markdown page.

Add Breadcrumbs
To add a breadcrumb to your page, simply add <!-- add breadcrumbs --> to the top of your manual page

Page format for ERPNext docs
The recommended Format for ERPNext docs can be here.


##### Reference

[REF:Develop on github](https://github.com/frappe/frappe/wiki/App-Development-using-GitHub)
[REF:update Documentation](https://github.com/frappe/erpnext/wiki/Updating-Documentation)
