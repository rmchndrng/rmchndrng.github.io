**Setting up your own static website for free (Github pages + Jekyll)**
===================================================================
As a programmer, everyone has the urge to showcase their talent, skills and work online. The various platforms available for them to accomplish this is via Github, StackOverflow, Coding problem sites (Topcoder, CodeMonk, HackerEarth, CodeChef, HackerRank etc), blogs and by maintaining their own website.

First step to own your own website is to get yourself a domain from various domain providing sites like Godaddy, BigRock etc.(Will skip this for now.) Then comes the problem of hosting the site. Especially for free. Thankfully, [Github](https://github.com/) comes to the rescue. Github provides a free service by the name [Github Pages](https://pages.github.com/) by which you can host free static websites.

**Github Pages**
------------
The website will be hosted directly on your GitHub Repository. You can create one site per GitHub account and organization and unlimited project sites. User or organization site is the static site which is tied to your GitHub account where as the Project site is the static site which is associated to each of your GitHub project.

Follow the steps mentioned at https://pages.github.com/ to create the repository and the website.

If everything went correctly, your website will be hosted at http://username.github.io (eg. http://rmchndrng.github.io) (Remember, this is a publicly hosted repository, anyone can clone your website source code.)

**Blogging with Jekyll**
--------------------
Once you start blogging own your site, it becomes very difficult to maintain the static files after some period of time. This is where [Jekyll](http://jekyllrb.com/) comes to the rescue.

Jekyll is a simple, blog-aware, static site generator. It takes a template directory containing raw text files in various formats, runs it through a converter (like Markdown) and our Liquid renderer, and spits out a complete, ready-to-publish static website suitable for serving with your favorite web server. Jekyll also happens to be the engine behind GitHub Pages, which means you can use Jekyll to host your project’s page, blog, or website from GitHub’s servers for free.

Before pushing the changes of your Jekyll site, it is always recommended to build and run the Jekyll site locally on your machine, to make sure the html being rendered by the GitHub pages is exactly what we intended. For windows users it is somewhat difficult to setup Jekyll. There is an unofficial instructions written up by Julian Thilo which is quite helpful. http://jekyll-windows.juthilo.com/

**Jekyll Installation on Windows**
------------------------------

Follow the steps mentioned in http://jekyll-windows.juthilo.com/ to setup Jekyll locally.
Please find below the steps in short.

**Step 1: Install Ruby and the Ruby DevKit**
Install Ruby using the installer.
Extract the Ruby Dev kit to a specific folder and associated with the Ruby installation
	
	cd C:\RubyDevKit
    ruby dk.rb init
    ruby dk.rb install
    
**Step 2: Install Jekyll Gem**

    gem install jekyll

**Step 3: Install a Syntax Highlighter**
Install Rouge (An alternative to pygments.rb which requires Python)

    gem install rouge

Then, in your _config.yml, set Rouge as your syntax highlighter: (To be used later)

    highlighter: rouge

**Use Pygments as Syntax Highlighter**
**Install Python** - (Python 3 will not work, requires v2.7)
**Install pip** - (Pip is a tool for installing and managing Python packages.)
 To install pip, securely download get-pip.py
 Then run the following (which may require administrator access):
	
    python get-pip.py

**Install Python base of Pygments**

    python -m pip install Pygments

If it’s not set already, add the following to your _config.yml to set Pygments as your syntax highlighter.(To be used later)

    highlighter: pygments

**Step 4: Let Jekyll --watch**
**Install the wdm Gem**

    gem install wdm

**Create a new Jekyll site**
----------------------------------------------
Navigate to the folder in which the username.github.io repository was cloned through command prompt.

    jekyll new .
    
   This will create all the files required for a new jekyll site.
   To test the site locally, run the following command and navigate to the address shown in the command prompt.
  

    jekyll serve --watch

   Push or commit the changes into github servers.(Follow the Step 4 mentioned in https://pages.github.com)
   You can see the new Jekyll site you just created when you visit http://username.github.io
  
  There are lots of free Jekyll themes available which you can try out.
