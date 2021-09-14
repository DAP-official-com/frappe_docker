This is a short guide outlining the steps for contributing to the ERPNext documentation. The docs can be contributed by anyone, not just developers.

Pre-requisites
Note: Before starting to begin writing/editing/fixing the docs, you must have a little background in the following technologies:

1. Frappe
Just like ERPNext, the erpnext docs website is a Frappe app. We will be installing the app just like ERPNext on a bench site. This will be used to visually review the changes made to the docs.

In case of a Mac or Linux OS, you can setup Frappe on your laptop/PC by following these guides. In the case of a Windows machine, you can use a VM to install Frappe.

2. Git
Frappe/ERPNext, uses Git for revision control. You can learn the basics by referring to a multitude of free online tutorials. You can also brush up your skills on https://try.github.io/

The source code of ERPNext is hosted on GitHub, a hosting service using the Git SCM. Some other popular hosting services are Bitbucket and Gitlab. You can signup here and create an account for free.

3. Markdown
Markdown is a lightweight markup language (like HTML) which is converted to structurally valid HTML when you see a webpage. Markup lets you format the text much faster than HTML by avoiding the tags and using special symbols for the same. Some parts of this wiki are written in the same way!

Learning markdown is pretty easy and you may have used it unknowingly when formatting messages on Whatsapp or on the forum.

To know how to write/format in markdown, you can always refer to a cheatsheet.

4. Creating your own fork of the ERPNext Documentation application
Getting into jargon, think of a fork as your copy of the application. The fork will be created on your GitHub profile/account just like mine is. This will be referred to as your fork or remote from hereafter.

To create a fork, simply go to the documentation repo, and click on the fork option:

Forking repo
Okay, now since we're pretty much done with the pre-requisites, let's start contributing some docs!

Editing Docs on GitHub
This method is to be used strictly in the case of editing the text of a particular page like say, adding more sentences or paragraphs, fixing spelling mistakes, fixing the link of an image or webpage, add a missing link in the index, etc.

This cannot be used when you need to add a new page or a new image.

1. Locate the page to be edited
You can do this by simply clicking on the link saying Improve this page

ERPNext Documentation - Locating the page

The alternative, you can go directly to the GitHub repo where the documentation is hosted: https://github.com/frappe/erpnext_documentation

2. Edit the page
After clicking the link, you will be taken to the specific page on GitHub which you can edit by clicking on the Pencil icon:

ERPNext Documentation - editing on Github ERPNext Documentation - editing on Github
PS: Make sure to follow the format for writing the documentation!

3. Sending Pull Request
Once the changes have been made, click on the green button at the very bottom saying Propose File Change and raise a pull request.

ERPNext Documentation - Raising PR
And there you go. You have raised a pull request to make changes to the documentation!

Making Changes to Files Locally
Note: You should have Bench installed locally for this.

Following are the steps to make changes in the documentation locally:

1. Download the Documentation App
Run the following commands in your frappe-bench folder via terminal:

To check if the docs app is present in your apps folder type ls apps

The response should look like following (view will change depending on the OS):

ERPNext Documentation
If you do not see erpnext_documentation anywhere, you can call it simply by the following command:

bench get-app https://github.com/frappe/erpnext_documentation

This will download the erpnext_documentation app within your apps folder.

In the same manner, we will download the frappe_theme app which contains the theming of the website.

bench get-app frappe_theme

2. Install New Site And The App
Our next step is to simply create a new site and install both apps on them. First, we need to start bench in a different tab of the terminal with bench start. Coming back to the previous tab run the following commands to create a new site and install the apps on the same.

bench new-site site-name
bench --site site-name install-app erpnext_documentation

You can go to your hosts file to add the new site name to the same. Refer to this link for the same.

Go to your browser and visit the new site created by typing the site name in the address bar: site-name:8000.

Congratulations. You are now running the erpnext website on your local bench!

ERPNext Documenttion Website
3. Edit the docs
For beginning to edit the docs, it is best to create a separate branch where these changes are made. You can learn more about Git Branches here. When you create a separate branch, any changes made here will not affect the master (or main) branch.

To create a new branch, follow the following steps:

cd apps/erpnext_documentation This will take you to the documentation app directory from frappe-bench
git branch branch-name Creating a new branch
git checkout branch-name Switch to new-branch from master branch

Or, if you want to do it in one go:

git checkout -b branch-name

The next step is to edit the .md files. You can use any code editor available online such as VS Code, Atom, Sublime Text, or Vim.

Editing ERPNext Docs Locally
When adding a new page, make sure to add the title in the index.txt file. This will add the page in the index listing of the directory.

Golden Rule: Whenever in doubt, refer to the existing pages.

In the same manner, when adding the documentation for a module (like how Accounting is), make sure to add the index.md page, which is the basic introduction of the module which will again the index.txt for the individual pages/topics under the same.

Note: For the formatting of the documentation pages, go through this page

If you're adding any new images, you add them in the docs/assets directory which has a folder for each module.

Once this is done, you can review your changes by refreshing the documentation page on your browser.

Note:Be sure to add the following to your common_site_config.json fie located under sites folder so the changes are loaded immediately and you do not have to restart bench for viewing the changes:

"disable_website_cache": 1,
"developer_mode": 1,


4. Commit Changes
Once the changes are made and you are satisfied with the same, you need to commit those changes. Run these commands in the erpnext_documentation app folder:

git add . Preparing modified file for commit
git commit -m 'enter commit message here' Commit the changes. Save in local repository

Once these changes are committed, you are near to making a pull request, the final step of making the contribution.

5. Push to your remote
Now that the changes have been saved, it is time to push it to your remote repository on GitHub. We will use a simple git push command for the same:

git push https://github.com/myaccount/erpnext_documentation branch-name

Note: You can also create a new remote name instead of writing down the full address. Refer this link for more information.

Also, if you are getting an error saying something like: "! [remote rejected] master -> master (shallow update not allowed)" run the following command and then push again:

git fetch --unshallow https://github.com/frappe/erpnext_documentation

This warning will show up only when making changes and pushing the changes for the first time.

6. Making the Pull Request
Once these changes have been pushed to the remote, you will see a green button (saying Compare & Pull Request) on your remote's page to make a pull request:

Compare and Pull request Making pull request
Clicking on the option will take you to the pull request screen where you can write the description for the same.

Here, do define in brief what changes you have made. Adding images (before and after) do help.

Note: In case of translations, it would be great if they can be verified by another user or someone on the forum. This will help us deliver a better experience to users worldwide

And voila! You have made a contribution to ERPNext!

Seinfeld Gif
PS: To contribute to the frappe framework documentation, follow the same steps with the frappe_io app




[How To Contribute to the ERPNext Documentation (The Lengthy Version) REF:](https://github.com/frappe/erpnext/wiki/How-To-Contribute-to-the-ERPNext-Documentation-(The-Lengthy-Version)
