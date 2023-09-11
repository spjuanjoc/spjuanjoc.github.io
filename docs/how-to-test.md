---
title: "Tests"
sitemap: false
---

# How to test the site locally

## Install the tools

1. Install build dependencies and Ruby:

    ```shell
    sudo apt install -y build-essential zlib1g-dev ruby-full
    ```

2. Configure Ruby gems not to be installed as root:

    ```shell
    echo '# Install Ruby Gems to ~/gems' >> ~/.zshrc
    echo 'export GEM_HOME="$HOME/gems"' >> ~/.zshrc
    echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.zshrc
    source ~/.zshrc
    ```

3. Install the Ruby gems: Jekyll and bundler:

    ```shell
    gem install jekyll bundler
    bundle install
    ```

## Test the site

Every time the `config.yml` is modified the project needs to be rebuilt:
Install the project's gems from its root:

```shell
bundle; bundle update; bundle exec jekyll build
```

Run the server from the root of the repository:

```shell
bundle exec jekyll serve
```

The site will be locally available at:

    `Server address: http://127.0.0.1:4000/`


## Troubleshooting

1. 
    > "Jekyll command not found":

    Make sure that the `$GEM_HOME` is exported and pointing to the user's home.

        echo $GEM_HOME

2. 
    > GitHub Metadata: No GitHub API authentication could be found.

    Some fields may be missing or have incorrect data.":
    From [asnowfix](https://github.com/github/pages-gem/issues/399#issuecomment-301827749)

    - Add to the `_config.yml`

           github: [metadata]

     - Run install

           bundle install; bundle update

     - Run serve

           bundle exec jekyll serve

3.
    > bundler: failed to load command: jekyll (/home/user/gems/bin/jekyll)
    > LoadError: cannot load such file -- webrick
    > bundler: failed to load command: jekyll 
    > LoadError: cannot load such file -- webrick

    The gem `webrick` may be missing.  
    Add `gem "webrick"` to the Gemfile.


## Add MathJax

See 
[cross-validated](https://www.cross-validated.com/How-to-render-math-on-Minimal-Mistakes/),
[janmeppe](https://www.janmeppe.com/blog/How-to-add-mathjax-to-minimal-mistakes/),
[pwills](http://www.pwills.com/posts/2017/12/20/website.html)
posts. To avoid modifying the file `scripts.html`, the same can be done
modifying the `_includes/head/custom.html` file.

To create an equation with a helper see this LaTeX [eq editor](https://www.codecogs.com/latex/eqneditor.php).
