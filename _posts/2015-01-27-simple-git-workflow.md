---
layout: post
title: "A simple and effective local Git workflow"
date: 2015-01-27T01:46
comments: true
sharing: true
categories:
 - technology
---
Git is a wonderful piece of software, I really love it. It permeates my daily routine so much that I can't even imagine how I could work without it. On the other hand, Git has been, for many developers, initially a bit hostile, because it opens a huge amount of possibilities and it's easy to misuse it. So, after a bit of experience, I developed a personal workflow which helps me using the tool more effectively.

## Scenario

Let's imagine a scenario where we have a shared branch and developers are committing code to it. It could be the `master` or any other published branch, for example, but this depends on the overall Git workflow, while this post is only about the individual developer's workflow. In our case, we usually contribute to a common branch named `develop` where we keep the SNAPSHOT releases (yes, we use Maven), while we only merge into master when we want to release a stable milestone of our software.

In general I recommend to **never commit directly to a public branch**, but always work with a local, not published branch, only allowing periodic merges into any public branch. In our routine we use JIRA for issues tracking and we usually name branches with the related JIRA issue numbers. Whatever works for you it's fine.

> commit to local branches, contribute to published branches only by __merging__ into them

Periodically I check the remote `develop` branch for new contributions. The frequency depends a lot on the team's size and average amount of these contributions. Hopefully there will be some coordination, in order to partition the work among different people, or sub-teams, so that overlapping is minimized. However, we don't take this for granted, especially we don't always know for sure when somebody is refactoring code which might affect shared components.

Pulling from the remote branch frequently doesn't hurt anybody and allows to be aware of what's going on in other teams, so there are no good reasons to delay it. Having a **Continuous Integration** process in place helps a lot on this side, as we receive automatic notifications each time somebody pushes a new piece of code into remote branches.

The first mandatory step before I can create a new branch is to checkout the public branch I want to contribute to, and to pull all the available modifications. Let's say the `develop` branch is the remote one, just for this example:

{% highlight console %}
$ git checkout develop
$ git pull origin develop
{% endhighlight %}

Now I can create a new branch, let's call it _local_ for the sake of this example:

{% highlight console %}
$ git checkout -b local
{% endhighlight %}

The above command creates the branch and also checkout to it, so it becomes the active one locally.

Then imagine I'm _adding code, commit, code, commit_, and so on, on the `local` branch.

At a certain point I will want to merge my branch into the selected remote (`develop` in this example), to make my changes available to everybody.

First of all, I again issue the checkout & pull commands on `develop`, so that my local copy of it is the most recent. Second, I checkout my `local` branch again and **rebase** it on top of `develop`:

{% highlight console %}
$ git checkout develop
$ git pull origin develop
{% endhighlight %}

 And

{% highlight console %}
$ git checkout local
$ git rebase develop
{% endhighlight %}

The last command above rewrites the local branch history, so that its commits will appear on top of `develop`'s. That's why __you'll never want to rebase any published branch__ as this would mess up with its published history, creating a lot of issues to other developers.

> __Don’t Rebase Public History__. You should never rebase commits that have been pushed to a public repository. The rebase would replace the old commits with new ones, and it would look like that part of your project history abruptly vanished.

One of the most clear description of the rebasing process [is available from Atlassian](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase/):

> From a content perspective, __rebasing__ really is just moving a branch from one commit to another. But internally, Git accomplishes this by creating new commits and applying them to the specified base — it’s literally rewriting your project history. It’s very important to understand that, even though the branch looks the same, it’s composed of entirely new commits.

The immediate benefit is that the whole source code's history is now linear

> You have two options for integrating your feature into the master branch: merging directly or rebasing and then merging. The former option results in a 3-way merge and a merge commit, while the latter results in a fast-forward merge and a perfectly linear history.

Last step is to merge the rebased branch into develop and push it, so that our local modifications are available to everybody:

{% highlight console %}
$ git checkout develop
$ git merge local
$ git push origin develop
{% endhighlight %}

 If you are very unlucky and meanwhile somebody has pushed a conflicting commit, the `git push` command will fail and you'll have to resolve conflicts locally before trying again.

As explained, we'll get three main benefits:

1. Linear, clean commit history of the remote branch;
2. Merge conflicts are resolved while trying to rebase into `develop`, so when the active branch is the `local` one and not when it's the remote one.
3. The final merge of `local` into `develop` is a trivial fast-forward, as all possible conflicts have already been resolved.

Then we can safely delete the local branch, as all its code has now been merged into its final destination.

{% highlight console %}
$ git branch -d local
{% endhighlight %}

The `-d` in lowercase is a safety net which, before deleting the branch, checks that it has been successfully merged already.

Of course, sometimes there could be some situations where keeping track of merges in the published history is a better option, for example when it's interesting to highlight that a sequence of commits is coming from a a specific branch. Probably this kind of tracking could be better achieved by _squashing_ all the incoming commits into one and adding a tag, but this will be a good topic for another post.

## In summary

The complete sequence of steps:

 1. `git checkout develop`
 1. `git pull origin develop`
 1. `git checkout -b local`
 1. Code, commit, code, commit, code, commit, ...
 1. `git checkout develop`
 1. `git pull origin develop`
 1. `git checkout local`
 1. `git rebase develop`
 1. Resolve conflicts, if any;
 1. `git checkout develop`
 1. `git merge local` (no conflicts at all, always plain fast-forward merge)
 1. `git push origin develop`
 1. Resolve additional conflicts, only in case somebody pushed a second after your previous pull and that generates a conflict (unlikely event);
 1. `git branch -d local`
