# Onefootball blog, notes to conributors

The website is accessible at https://onefootball.github.io/. It uses the GitHub Pages system to publish static pages generated with Jekyll. The installation and configuration has been done with that tutorial http://www.smashingmagazine.com/2014/08/01/build-blog-jekyll-github-pages/.

## General structure of the project

  * All blog posts are in the `_posts/` directory. The naming pattern should be `YYYY-MM-DD-$title.md`.
  * If you want to tweek the theme, checkout the markup in `_layouts/` and the CSS/SASS in `_sass`
  * Images should be stored in `images/` and grouped by date (YYYY-MM).
  * Linking to images from a post is done with the `site.baseurl` variable: `![_config.yml]({{ site.baseurl }}/images/2015-07/hacker.jpg)`


## How to contribute

  * fork this repository
  * clone your fork locally
  * branch, do your changes, commit, and open a pull request to `Onefootball:master`

### Intalling Jekyll (needed to test locally)

See https://help.github.com/articles/using-jekyll-with-pages/#installing-jekyll for a full documentation.

#### Ruby

In a terminal, run this:

```bash
ruby -v
```

If your version is `> 2.1` everything's fine, if not you'll need to update Ruby.

**On Mac**, first you'll need to install [brew](http://brew.sh/):

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Then you can install RVM (Ruby Versions Manager)

```bash
\curl -L https://get.rvm.io | bash -s stable --ruby
```

And finally update your Ruby

```bash
rvm install 2.1.5 --autolibs=enable
source /Users/{your_user_name}/.rvm/scripts/rvm
```

See a full documentation here https://coderwall.com/p/4imsra/upgrading-ruby-to-2-1-0-and-above-in-mavericks

**On Linux** (Debian):
```bash
rvm install 2.1.5 # needs >2
ruby --version # if necessary `$ rvm use 2.1.5`
```

**On Windows**: TODO
You have to have Python with Pygments installed and globally accessible.


#### Gem

```
  $ cd $project_dir
  $ gem install bundler
  $ bundle install
```

You're ready!


### Running

```
  $ git checkout master
  $ bundle exec jekyll serve
```

Then open your browser on `http://localhost:4000`.

### The syntax

Post have to be written using the [GitHub flavored Markdown](https://help.github.com/articles/github-flavored-markdown/). If you have not installed Jekyll locally you can use [StackEdit.io](https://stackedit.io/).

### The Authors file

If it's your first contribution, consider adding your name to the `_data/authors.yml` file:

```yml
yourname:
  name: Yøür Nąmé
  twitter: YourNameOnTwitter
```

You can then use the key `yourname` in the `author_id` field of your post.
