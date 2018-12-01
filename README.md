#The Ask Yammer Blog

This blog is built off of Hugo and hosted through GitHub.

##Getting Started

### Must Have:
* [Git For Windows installed](https://git-scm.com/download/win)
* A basic understanding of GitHub
* A basic understanding of VIM (or other text editors like [Sublime](https://www.sublimetext.com/) or [Brackets](http://brackets.io/)
* You are employed by Microsoft as a Yammer Support Escalation Engineer


### Install all the things

1. Follow [this guide](https://gohugo.io/getting-started/installing/#windows) to get Hugo Installed
2. Follow [these instructions](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) to generate a new SSH key
3. Follow [these instructions](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/) to add the SSH key to your GitHub account

### In Git for Windows

`git clone https://github.com/askyammer/askyammer.github.io.git`

#### Congratulations! You now have the repro on your machine and ready for you to post.

## Posting a new blog entry

So you'd like to post a new entry. Let's talk about that.

When you post a new entry to the blog, there are several things that you have to make sure you do:

* Images need to be in the static/img folder, and should be in it's own folder that matches the name of the post you're authoring
* The post that you author must have the below naming scheme:
YYYY-MM-DD-This-Is-My-Post.md
* [Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) is a good thing to use for posts.

### Creating a post
You need to be in the GitHub/askyammer.github.io/AskYammer folder

`hugo new post/YYYY-MM-DD-This-Is-My-Post.md`

At this point you can either edit inside of Git using VIM, or you can open the file with your preferred editor

* The post will now be in askyammer.github.io/AskYammer/content/post/

* View the other posts (particularly **Yammer API Primer** for examples on how to write a good post, and what the markdown looks like.

* When adding an image, you'll want to use the following format:

`![WHATEVERTHEIMAGEIS](/img/FILEYOUMADE/PICTUREWITHEXTENSION)`

### Sending the post to GitHub
Now for the fun part!

You can see what the post will look like by using the following:

* Make sure that you are in the AskYammer directory, and not the askyammer.github.io directory

`hugo`

`hugo server`

Now navigate to localhost:1313 in a browser and you should see the post listed there. You can click on it and see what it looks like. 

If you make changes to the file while Hugo Server is running, it will show you a live preview

**Now**

Ctrl+C

`hugo`

`./deploy.sh`

Now it should deploy to GitHub and make the whole thing live!

## HELP ME
Ping Dan Long in Yammer and I should be able to assist you :heart:
