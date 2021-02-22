# How to test the site locally

## Install the tools

1. Install build dependencies and Ruby:  
    `sudo apt install -y build-essential zlib1g-dev ruby-full`

2. Configure Ruby gems not to be installed as root:  
````shell
    echo '# Install Ruby Gems to ~/gems' >> ~/.zshrc
    echo 'export GEM_HOME="$HOME/gems"' >> ~/.zshrc
    echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.zshrc
    source ~/.zshrc
````

3. Install the Ruby gems: Jekyll and bundler:  
    `gem install jekyll bundler`

## Test the site

Every time the config.yml is modified the project needs to be rebuilt:
Install the project's gems from its root:  
   `bundle; bundle update; bundle exec jekyll build`

Run the server:  
   `bundle exec jekyll serve`

The site will be locally available at:  
    `Server address: http://127.0.0.1:4000/`

## Troubleshooting

- "Jekyll command not found":  
    - Make sure that the `$GEM_HOME` is exported and pointing
to the user's home.  
        `echo $GEM_HOME`  


- "GitHub Metadata: No GitHub API authentication could be found. 
Some fields may be missing or have incorrect data.":
From [asnowfix](https://github.com/github/pages-gem/issues/399#issuecomment-301827749)  
    - Add to the `_config.yml`  
        `github: [metadata]`

    - Run install  
        `bundle install; bundle update`

    - Run serve  
        `bundle exec jekyll serve`

