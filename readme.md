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

## How to deploy 
Currently the Github.com/costicaaa/hugo-blog repo is linked with Netlify through the official GitHub - Netlify app and it builds and publishes the blog each time a commit is pushed to Github.

## Draw Io export svg with background
Embed images 
Text settings: Embed Fonts

