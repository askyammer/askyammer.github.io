# The Ask Yammer Blog

This blog is built off of Hugo and hosted through GitHub.

## Table of Contents
- [Getting Started](#getting-started)
  * [Must Have](#must-have)
  * [Install all the things](#install-all-the-things)
  * [In Git for Windows](#in-git-for-windows)
- [Posting a new blog entry](#posting-a-new-blog-entry)
  * [Creating a post](#creating-a-post)
  * [Sending the post to GitHub](#sending-the-post-to-github)
- [HELP ME](#help-me)

## Getting Started

### Must Have
* [Git For Windows installed](https://git-scm.com/download/win)
* A basic understanding of GitHub
* A basic understanding of VIM (or other text editors like [Sublime](https://www.sublimetext.com/) or [Brackets](http://brackets.io/))
* You are employed by Microsoft as a Yammer Support Escalation Engineer


### Install all the things

1. Follow [this guide](https://gohugo.io/getting-started/installing/#windows) to get Hugo Installed
2. Follow [these instructions](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) to generate a new SSH key
3. Follow [these instructions](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/) to add the SSH key to your GitHub account

### In Git for Windows

#### Never cloned the repository
`git clone https://github.com/askyammer/askyammer.github.io.git`
> This command tells `git` to `clone` what we have hosted on GitHub for the `askyammer.github.io` project. When you clone a project, it then will live natively on your system. This should only be done initially and not everytime you need to make a post

#### Previously cloned the repository
`git pull origin master`
> This command tells `git` to `pull` the `origin` from `master`. By doing this, you will update your locally hosted files on your machine with what GitHub already has. This is helpful so you don't overwrite another persons changes that they may have made with the repository. You should always pull before posting a new item to the blog

#### Congratulations! You now have the repro on your machine and ready for you to post.

## Posting a new blog entry

So you'd like to post a new entry. Let's talk about that.

When you post a new entry to the blog, there are several things that you have to make sure you do:

* Images need to be in the static/img folder, and should be in their own folder that matches the name of the post you're authoring
* The post that you author must have the below naming scheme:
YYYY-MM-DD-This-Is-My-Post.md
* [Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) is a good thing to use for posts.

### Creating a post
You need to be in the GitHub/askyammer.github.io/AskYammer folder

`hugo new post/YYYY-MM-DD-This-Is-My-Post.md`

At this point you can either edit inside of Git using VIM, or you can open the file with your preferred editor

* The post will now be in askyammer.github.io/AskYammer/content/post/

* View the other posts (particularly [**Yammer API Primer**](https://github.com/askyammer/askyammer.github.io/edit/master/AskYammer/content/post/2017-09-05-Yammer-API-Primer.md) for examples on how to write a good post, and what the markdown looks like)

* When adding an image, you'll want to use the following format:

`![WHATEVERTHEIMAGEIS](/img/FILEYOUMADE/PICTUREWITHEXTENSION)`

### Sending the post to GitHub
Now for the fun part!

You can see what the post will look like by using the following:

* Make sure that you are in the AskYammer directory, and not the askyammer.github.io directory

````
hugo
hugo server
````
> The first command (`hugo`) tells Hugo to build out the site in the root directory. The second command (`hugo server`) starts the server process

Now navigate to [localhost:1313](http://localhost:1313) in a browser and you should see the post listed there. You can click on it and see what it looks like. 

If you make changes to the file while Hugo Server is running, it will show you a live preview

**Now**

Ctrl+C

````
hugo
./deploy.sh "New Blog Post"
````
> The second command (`./deploy.sh "New Blog Post"`) is a script that will go through and push your changes to master here in GitHub. By having `"New Blog Post"` written out shows what was changed to Master so we know what kind of stuff you pushed out.

Now it should deploy to GitHub and make the whole thing live!

## HELP ME
Ping [Dan Long](https://www.yammer.com/microsoft.com/#/users/1510044728) in Yammer and I should be able to assist you :heart:
