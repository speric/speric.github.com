---
layout: post
title: My git Workflow
header_img: "git.jpg"
---
<p>I've settled into what I think is an effectively simple git workflow that I thought would be helpful to share.</p>

<p>Let's say I am working on a new feature. First thing I want to do is
make sure my local copy of the codebase is up-to-date:</p>

<pre>$ git pull master</pre>

<p>I'll then create a new feature branch. In my experience, 99% of the
time I am branching off of master:</p>

<pre>$ git checkout -b an-awesome-new-feature</pre>

But it's just as easy to branch off another branch:

<pre>$ git checkout -b an-awesome-new-feature some-other-branch</pre>

<p>Within this new branch, I can spike on the new feature, and if things
get hairy I can blow it away and start over. I'll commit often. Most of
my commits during the spike or early phase of a feature will look
like:</p>

<pre>$ git commit -am "WIP specs"</pre>

<p>If I am
pairing, I will be more descriptive with the commit messages. But when I am
coding alone, these pre-PR commits are for me, and are not meant to be
seen by my colleagues. When I am satisfied that the work I've done will
eventually become a PR, I'll push my branch:</p>

<pre>$ git pushr</pre>

<p>The <code>pushr</code> command is a nice little shortcut from the <a
href="https://github.com/jeremywrowe/dotfiles/blob/master/dots/gitconfig#L67">dotfiles</a> I use. It creates a remote branch with the same name as my local branch, in this case, <code>an-awesome-new-feature</code>. After the initial <code>$ git pushr</code>, I can just do <code>$ git push</code> whenever I'm push to the remote branch.</p>

<p>The "do some work, test, commit, push" loop happens until I am
satisfied that the feature is ready for a pull request. If a new feature
was merged into <code>master</code> that will effect my feature, I'll
pull the changes in, and then do:</p>

<pre>$ git rebase master</pre>

<p>Recently I've gotten into the habit of <a
href="http://git-scm.com/book/en/Git-Branching-Rebasing">rebasing</a> my
feature branches whenever anything is merged into <code>master</code>,
even if I don't think they will effect what I am doing. I've done this
mainly because, if there is a conflict, I'd rather be in merge hell in
the middle of working on a feature than at the end of a feature. It's dispiriting when you're about to open a shiny new PR and you realize it won't merge cleanly.  It's a psychological thing!</p>

<p>Now, my commit
history will be made up of all "WIP" commits, which is to say, the
history will be messy and the messages be unhelpful.</p>

<p>At this point I
will <a href="http://gitready.com/advanced/2009/02/10/squashing-commits-with-rebase.html">rebase
and squash</a> all of my commits on the
<code>an-awesome-new-feature</code> branch into one commit. This commit
message will be as informative as possible, since it will become the
basis for the pull request message.</p>

<p>Once that's done, I'll force push to the branch with my new rebased
history:</p>

<pre>$ git push --force</pre>

<p>I'll then open my pull request. I will push new commits to the branch
after the PR is open, based on feedback. Once I get a :shipit:, I like
to re-squash all post-PR commits back into the first PR commit.</p>

<p>There's
been some discussion on our team about the merits and demerits of this
approach.  I like to have one commit per PR when it's possible. It seems
cleaner to me. But there's a good argument for not doing this, since
those post-PR commits were changes made based on feedback, and that
feedback history is important to have. Personally, I feel the comments
in the PR on GitHub are sufficient as far as history goes. Usually if I
want to dig deeper into what went into a new feature and why, I'll go back and re-read
the PR message and subsequent comments, as opposed to reading commit messages in <code>git log</code>. But that's my
own preference. The other way is fine too, in my opinion.</p>

<p>In either case, once the PR is merged, the feature branch is deleted
locally and remotely. The <code>master</code> is updated locally via
<code>git pull</code>, and the cycle repeats.</p>
