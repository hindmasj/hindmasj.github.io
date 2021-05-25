# Hindmasj And His Uncertain Ramblings

Source code stored on [GitHub](https://github.com/hindmasj/hindmasj.github.io).

# Content

## Tumblr

I used to maintain a [tumblr site](https://open-badger.tumblr.com/). I have not posted there in a long time but there are some nice pictures. I might start to move the best bits over to here.

## Technical Posts

Some of the themes to come are on Emacs, Ansible and Docker.

## Other Posts

I want to see how versatile this site is. Can I post external content, like from my [Flickr Account](https://www.flickr.com/photos/55891150@N00/)? We shall see.

# Working Directory Testing

This website is build with [GitHub Pages](https://docs.github.com/en/pages) and works by allowing you to edit in Markdown and then after you commit, GitHub uses [Jekyll](https://jekyllrb.com/) to convert the pages to HTML.

So if you want to test the pages before you commit them you need to use Jekyll in the working directory to create the content that will actually be viewed. Use these steps to install and run Jekyll on Fedora.

The [GitHub instructions](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll).

## Installation

````
sudo dnf install gcc make ruby rubygems
sudo dnf install ruby-devel openssl-devel redhat-rpm-config @development-tools
sudo gem install jekyll bundler github-pages
````

## Setup

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

## Usage

Run ``bundle exec jekyll serve``.

Navigate to [http://localhost:4000] to see the web page.

If like me you are using a VM (WSL2) for your development server, you need to pick a different listening address, then access port 4000 on that address.

````
ip addr
bundle exec jekyll serve --host xx.xx.xx.xx
````

## Git

Add the gem file and ignore the lock file and site directory.

````
git add Gemfile
echo Gemfile.lock >> .gitignore
echo _site >> .gitignore
git add .gitignore
````

Then commit and push as you need.
