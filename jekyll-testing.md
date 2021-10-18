# Testing The Site From Inside The Working Directory

This page details how to test the site from within the working directory, instead of having to publish every change before you see how it looks.

## In Brief For WSL2

If you have completed the installation and config steps then every time you want to see your changes then do this. The script includes a trick to get the primary address of the host.

````
HOST_IP=$(ip addr show up primary scope global | awk '/global/ {print gensub("/.*","","1",$2)}')
bundle exec jekyll serve --host ${HOST_IP}
````

On WSL2 you can "ctrl-click" the URL that the Jekyll command shows to open the page directly in your default browser.

When done, "ctrl-c" to finish. The pages are dynamically regenerates whenever the source file is saved, so you do not need to tear down and restart Jekyll after every change.

## The Background

This website is build with [GitHub Pages](https://docs.github.com/en/pages) and works by allowing you to edit in Markdown and then after you commit, GitHub uses [Jekyll](https://jekyllrb.com/) to convert the pages to HTML.

So if you want to test the pages before you commit them you need to use Jekyll in the working directory to create the content that will actually be viewed. Use these steps to install and run Jekyll on Fedora.

The [GitHub instructions](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll).

## The Detail

### Installation

````
sudo dnf install gcc make ruby rubygems
sudo dnf install ruby-devel openssl-devel redhat-rpm-config @development-tools
sudo gem install jekyll bundler github-pages
````

### Setup

Change directory to the working directory of your website.

````
bundle init
bundle install
````

Among other things this creates a "Gemfile" in the current directory. Configure required gems in this file. See this [useful Stackoverflow post](https://stackoverflow.com/questions/58598084/how-does-one-downgrade-jekyll-to-work-with-github-pages) which explains this configuration.

````
gem "jekyll", "~> 3.8.5"
gem "github-pages","~> 202" , group: :jekyll_plugins
group :jekyll_plugins do
  gem "jekyll-theme-modernist", "~> 0.1.1"
end
````

Then try

````
gem pristine racc --version 1.5.2
bundle install
````

### Git

Add the gem file and ignore the lock file and site directory.

````
git add Gemfile
echo Gemfile.lock >> .gitignore
echo _site >> .gitignore
git add .gitignore
````

Then test, commit and push as you need.

### Usage

Run ``bundle exec jekyll serve``.

Navigate to [http://localhost:4000] to see the web page.

If like me you are using a VM (WSL2) for your development server, you need to pick a different listening address, then access port 4000 on that address.

````
ip addr
bundle exec jekyll serve --host xx.xx.xx.xx
````
