# First commit 

## Initialize hugo directory and repository 

### Info 

> hugo version 
> > hugo v0.109.0+extended darwin/amd64 BuildDate=unknown


### Theme install

> git submodule add --depth=1 https://github.com/canhtran/maverick.git themes/maverick

> echo "theme = 'maverick'" >> config.toml

Check everything works
> hugo server

Add a new post
> hugo new posts/my-first-post.md

### Archetype setup & test 

Create new post with images

> hugo new --kind post-bundle posts/my-2nd-post
