---
layout: post
title:  "Binaries done right with git."
categories: blog
tags: [share, code, life, git, git-lfs]
updated: 2019-06-30
caption: But done wrong with Github pages.

gitlfs: https://git-lfs.github.com/
# todo: link to the next git study.
---
The past few days, I dedicated some time to studying about how to use git
better and how some of the git internals work.

My study started because of creating this very [blog]({{site.url}}). To ease
the creation of blog posts and maintaining the website, I wanted to store all
my images in the same GitHub repository. But I knew there is an inherent
problem with this. Git saves the history of every commit. If I edit and commit
one of the images contained in my blog's [GitHub
repository](https://github.com/alanxoc3/alanxoc3.github.io), git will still
store the previous unneeded version in the `.git` folder. Although I probably
won't edit enough binary files for this to be a big problem, it is still a
minor problem for a few reasons:
- Large unused git objects (blobs) will waste space on my [little netbook]({%
  post_url 2019-06-30-a-brave-little-netbook %}).
- Clone/Push/Pull times could be slower.
- I wanna know if there is a better way.

After a few google searches, I came across [`git-lfs`]({{ page.gitlfs }}). Here
is the description taken from the website: 
> Git Large File Storage (LFS) replaces large files such as audio samples,
> videos, datasets, and graphics with text pointers inside Git, while storing
> the file contents on a remote server like GitHub.com or GitHub Enterprise.

Cool! This takes away version control for my large binary files, but keeps them
in git, for GitHub to accept. That's exactly what I wanted.

The install is straightforward for [Arch
Linux](https://www.archlinux.org/packages/community/x86_64/git-lfs/). After
installing, it's really easy to set up for a __brand new repository__. Check out
the [website]({{ page.gitlfs }}) for that.

But wait!!!! __I don't have a brand new repository.__ And my repository already has
a bunch of binary files. What can I do?

Don't worry anxious reader, `git-lfs v2.2.1` or later makes it really easy to
migrate a repository! After running a `git lfs install` on the repo, use this
script taken from one of the [official
tutorials](https://github.com/git-lfs/git-lfs/wiki/Tutorial#migrating-existing-repository-data-to-lfs):

{% highlight bash %}
git lfs migrate import --include="*.png" \
    --include-ref=refs/heads/master \
    --include-ref=refs/heads/other_branch
{% endhighlight %}

Note: You should replace `png` with your file extension of choice (`png`, `jpg`,
`mp3`, etc). And each `--include-ref` line should correspond to each branch on
your server.

Afterwards, run a `git lfs ls-files` to see if your existing files are stored
by `git-lfs`. If they are, then you are good to go my kind sir :).

Oh wait, no you're not. Remember that you will need to run a `git push
--force`, since you are going to rewrite history on your git host server. Don't
you just love rewriting history?

__Disclaimer__. Even though the idea of `git-lfs` is cool, after trying to make
it work for my GitHub blog, I found out that GitHub Pages [doesn't support
`git-lfs`](https://github.com/github/pages-gem/issues/515)! I guess I will have
to revert my hard work and go back to using just plain git for now. I'll edit
this post if GitHub Pages ever supports `git-lfs`.

That's what I had to share for today.

Don't forget to __send me a message__ or __follow me below__ to keep me being a
motivated blogger!
