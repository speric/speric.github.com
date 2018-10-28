---
layout: post
title: "Cleaning Up Old Git Branches"
header_img: "lazarus.png"
description: "How to delete local branches whose remote has been deleted"
---

At work, we typically delete remote branches after they are merged into `master`. This often leaves me in a situation
where I have many _local_ branches whose _remote_ no longer exists.

Some folks like to keep those branches around _just in case_. Because of the power of [git-reflog](https://git-scm.com/docs/git-reflog),
I don't think keeping old branches around is necessary.

In a perfect world, I'd `git branch -D` everytime a remote branch was deleted. But things often
get too busy to clean up, and who wants to remember _which_ branch was deleted? Can't I just tell Git to clean up
after itself?

Yes, you can!

We can do so via this function in our shell profile of choice (mine is `.zshrc`), which will "delete any local branches whose remote has been deleted":

```zsh
clean_branches() {
  git remote prune origin
  git branch -vv | grep "origin/.*: gone]" | awk '{print $1}' | xargs git branch -D
}
```

Let's walk through it.

### Pruning

```zsh
git remote prune origin
```

The [docs](https://git-scm.com/docs/git-remote#git-remote-empruneem) for this command tell us that it:

> Deletes stale references associated with `<name>`

In this case, we want to delete stale references associated with the remote `origin`.

The best way to see what this command does is to compare before/after. Here is what `git branch -vv` looks like _before_ pruning:

```zsh
> git branch -vv
  bin-setup      d135a8d bin/setup
* clean-branches f1fdebb Merge pull request #12 from speric/tweaks
  gitignore      9fe5518 [origin/gitignore] Add jekyll-metadata to gitignore
  master         f1fdebb [origin/master] Merge pull request #12 from speric/tweaks
  tweaks         5de39cc [origin/tweaks] Tweaks
```

This tells me:
* I am on the `clean-branches` branch right now (writing this post!)
* I have a branch called `bin-setup` that is not tracking a remote branch
* Both the `gitignore` and `tweaks` branches _do_ track remote branches, of the same name (as does `master`, obviously)

I know that the `gitignore` and `tweaks` branches' remotes have been deleted, because I did so via the GitHub UI when I merged them into `master`.

Now, I will run `git remote prune origin`, and then `git branch -vv`:

```zsh
> git remote prune origin
Pruning origin
URL: git@github.com:speric/speric.github.com.git
 * [pruned] origin/gitignore
 * [pruned] origin/tweaks
> git branch -vv
  bin-setup      d135a8d bin/setup
* clean-branches f1fdebb Merge pull request #12 from speric/tweaks
  gitignore      9fe5518 [origin/gitignore: gone] Add jekyll-metadata to gitignore
  master         f1fdebb [origin/master] Merge pull request #12 from speric/tweaks
  tweaks         5de39cc [origin/tweaks: gone] Tweaks
```

We can see here that `gitignore` and `tweaks` have been pruned, which makes sense because their remote no longer exists.

Nothing happened to `bin-setup` because it never had a remote. Nothing happened to `master` either because it still exists at the remote.

Note that whereas, pre-prune, we saw the following for the `gitignore` branch:

```
gitignore      9fe5518 [origin/gitignore] Add jekyll-metadata to gitignore
```

Now we see that it's `gone`:

```
gitignore      9fe5518 [origin/gitignore: gone] Add jekyll-metadata to gitignore
```

With this information we can now build up our command to delete those remote branches.

### Deleting

```zsh
git branch -vv | grep "origin/.*: gone]" | awk '{print $1}' | xargs git branch -D
```

Let's take this set of commands apart.

```
git branch -vv
```

This command lists all local branches, [verbosely](https://git-scm.com/docs/git-branch#git-branch--vv). As we saw above,
after we've pruned, it will tell us which branches are "gone".

```zsh
grep "origin/.*: gone]"
```

The output of `git branch -vv` will be piped to the `grep` command above. This command will [grep](http://man7.org/linux/man-pages/man1/grep.1.html)
that output for any lines which contain `origin/some-branch-name: gone]`.

```zsh
> git branch -vv | grep "origin/.*: gone]"
  gitignore      9fe5518 [origin/gitignore: gone] Add jekyll-metadata to gitignore
  tweaks         5de39cc [origin/tweaks: gone] Tweaks
```

So now we have the two branches we want to delete: `gitignore` and `tweaks`.

```zsh
awk '{print $1}'
```

The output of our `grep`-ing is then piped to the above `awk` command. [awk](https://www.gnu.org/software/gawk/manual/gawk.html) is neat, and I confess I have
not grasped the full extent of its power.

In this context, we can think of the above code as saying: "For each line, in the piped-in text, print the first string". We can see what it does:

```zsh
> git branch -vv | grep "origin/.*: gone]" | awk '{print $1}'
gitignore
tweaks
```

We have isolated the _actual_ branch names. Nearly there!

```
xargs git branch -D
```

The [documentation](http://man7.org/linux/man-pages/man1/xargs.1.html) for `xargs` tells us:

> xargs reads items from the standard input, delimited by blanks (which can be protected with double or
single quotes or a backslash) or newlines, and executes the command (default is /bin/echo) one
or more times with any initial-arguments followed by items read from standard input.

`xargs` is awesome, and you should take the time to learn it if you're not already familiar with it.

Conceptually, we can think of our usage as "pass each item of the piped in text as the first argument to it's own invocation of `git branch -D`". We
will wind up running `git branch -D` twice in this case, once each for the `gitignore` and `tweaks` branches.

Put it all together, and we get:

```zsh
> git branch -vv | grep "origin/.*: gone]" | awk '{print $1}' | xargs git branch -D
Deleted branch gitignore (was 9fe5518).
Deleted branch tweaks (was 5de39cc).
```

Done!

### Wrap-up

Here's the function again; stick it in your shell profile of choice:

```zsh
clean_branches() {
  git remote prune origin
  git branch -vv | grep "origin/.*: gone]" | awk '{print $1}' | xargs git branch -D
}
```

I should also note that this kind of "workflow" only makes sense where you (or your team) get comfortable
deleting remote branches after they're merged.


Lastly, a shameless plug for my [dotfiles](https://github.com/speric/dotfiles).

### UPDATE
Shortly after this post went live, [@pengwynn](https://twitter.com/pengwynn) pointed me to a post he wrote
on [Extending the Command Line](https://wynnnetherland.com/journal/extending-the-command-line/). After digesting
that post, I've tweaked my approach here.

The big idea is that any file in your `PATH` that follows the `git-` convention will become a new Git command. With that knowledge, I added my dotfiles `bin` folder to the `PATH` in my `.zshrc`:

```zsh
path+=(
  ${HOME}/dev/dotfiles/bin
)
```
([https://github.com/speric/dotfiles/blob/5a2135c5fafb0dd783c1c81186b2f1e412897caf/zshrc#L14](commit))

Then, I moved the body of `clean_branches` to a file called `git-cleanup`:

```zsh
# !/bin/sh
# Usage: git-cleanup
# Delete any local branches whose remote has been deleted
git remote prune origin
git branch -vv | grep "origin/.*: gone]" | awk '{print $1}' | xargs git branch -D
```

I then deleted the `clean_branches` function from my `.zshrc`.

Now, in any repository, I can do:

```zsh
git cleanup
```

And the old branches will be deleted.
