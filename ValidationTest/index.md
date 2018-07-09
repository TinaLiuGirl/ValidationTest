---
title: GitHub contribution workflow for major or long-running changes
description: Shows you how to use the "major" contributor workflow to make contributions to docs.microsoft.com articles.
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 08/30/2017
ms.prod: non-product-specific
ms.topic: contributor-guide
ms.custom: internal-contributor-guide
---


# GitHub contribution workflow for major or long-running changes

## Overview
This article describes the full workflow to contribute to docs.microsoft.com using a local git repository and github.com.

This full workflow is needed when you contribute to documentation frequently, or for major sets of changes that include multiple documents.  Frequent contributors typically have ongoing and long-running changes, which go through multiple build/validation/staging cycles or span multiple days before pull requests are signed-off and merged. 

> [!TIP]
> You may refer external docs contributors to [this public contributor guide page](https://docs.microsoft.com/en-us/contribute/full-workflow) for similar information. Please help us keep the information in sync between these two pages as things change.

For Microsoft employees who are working on projects that use both public and private repositories, make these types of contributions to the private repository. Integrated OPS build, validation, and staging services are available in the private repository. 

Examples of these types of contributions include:

[!INCLUDE [contribute-how-to-write-workflows-major-change-definition](../includes/contribute-how-to-write-workflows-major-change-definition.md)]


> [!IMPORTANT] 
> All repositories that publish to docs.microsoft.com have adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information, see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/). Or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any questions or comments.<br>
>
> Minor corrections or clarifications to documentation and code examples in public repositories are covered by the [docs.microsoft.com Terms of Use](https://docs.microsoft.com/legal/termsofuse). New or significant changes will generate a comment in the pull request, asking you to submit an online Contribution License Agreement (CLA) if you are not an employee of Microsoft. You will need to complete the online form before your pull request can be merged.


### Terminology

Before you start, let's review some of the Git/GitHub terms and monikers used in this workflow. Don't worry about understanding them now. Just know that you will be learning about them, and you can refer back to this section when you need to verify a definition.

| Name | Description |
|-----------|-------------|
|fork|Normally used as a noun, when referring to a copy of a main GitHub repository. In practice, a fork is just another repository. But it's special in the sense that GitHub maintains a connection back to the main/parent repository. It's sometimes used as a verb, as in "You must fork the repository first."|
|remote|A named connection to a remote repository, such as the "origin" or "upstream" remote. Git refers to these as remotes because they are used to reference a repository that's hosted on another computer. In this workflow, a remote is always a GitHub repository.|
|origin|The name assigned to the connection between your local repository and the repository from which it was cloned. In this workflow, origin represents the connection to your fork. It's sometimes used as a moniker for the origin repository itself, as in "Remember to push your changes to origin."|
|upstream|Like the origin remote, upstream is a named connection to another repository. In this workflow, upstream represents the connection between your local repository and the main repository, from which your fork was created. It's sometimes used as a moniker for the upstream repository itself, as in "Remember to pull the changes from upstream."|

## Workflow

>[!IMPORTANT]
> If you haven't already, you must complete the steps in the [Setup](contribute-get-started-setup-github.md) section. This section walks you through setting up your GitHub account, installing Git Bash and a Markdown editor, creating a fork, and setting up your local repository. If you are unfamiliar with Git and GitHub concepts such as a repository or branch, please first review [Git and GitHub fundamentals](contribute-get-started-git-github-fundamentals.md).

In this workflow, changes flow in a repetitive cycle. Starting from your device's local repository, they flow back up to your GitHub fork, into the main GitHub repository, and back down locally again as you incorporate changes from other contributors. 

<!-- The following video will help you understand this flow at a high level:

<br>
<video width="640" height="360" controls poster="./media/contribute-how-to-write-workflows-major/your-individual-workflow.png">
  <source src="http://video.ch9.ms/ch9/1e1b/63477be7-a3df-4d25-b8e7-9d79df411e1b/githubcontributorworkflow_mid.mp4" type="video/mp4">
</video> 

-->

### 1. Create a working branch

Recall from [Git and GitHub fundamentals](contribute-get-started-git-github-fundamentals.md#git) that a Git repository contains a master branch, plus any additional work-in-progress branches that have not been integrated into master. Whenever you introduce a set of logically related changes, it’s a best practice to create a *working branch* to manage your changes through the workflow. We refer to it here as a working branch because it's a workspace to iterate/refine changes, until they can be integrated back into the master branch. 

Isolating related changes to a specific branch allows you to control and introduce those changes independently, targeting them to a specific release time in the publishing cycle. In reality, depending on the type of work you do, you can easily end up with several working branches in your repository. It's not uncommon to be working on multiple branches at the same time, each representing a different project. 

>[!TIP]
>Making your changes in the master branch is *not* a good practice. Imagine that you use the master branch to introduce a set of changes for a timed feature release. You finish the changes and are waiting to release them. Then in the interim, you have an urgent request to fix something, so you make the change to a file in the master branch and then publish the change. In this example, you inadvertently publish both the fix *and* the changes that you were holding for release on a specific date. 

Now let's create a new working branch in your local repository, to capture your proposed changes:

1. Start Git Bash.

2. Switch the context to your local repository, by using the Git Bash `cd <repo>` (change directory) command. Recall that your new local repository was created by the clone command, in a subdirectory under the path `c:\users\<user-account>\<repo>\`.

   ```
   cd <repo>
   ```

3. Let's pause at this point, and review some fundamental concepts that will be important as you learn how to use Git:

   a. **Working directory**: The previous `cd` command caused the Git Bash context to be changed to the repository directory that the clone command created. This repository is used for two purposes:

   - To provide a working directory, where you can make additions, changes, and deletions to a branch's files by using your Markdown editor and other programs. The files belong to the *current branch*.
   - To store the local repository. This is essentially a database where Git manages repository data structures, including the state of all of its branches, the remote "origin" and "upstream" connections, and other internal details. You use Git commands to transfer your files to/from the working directory and the Git repo database, instead of working directly in the repo database.  

   b. **Current branch**: The Git Bash context was also set to the branch in the local repo where your changes will be applied, referred to as the *current branch*. Git Bash defaults to the default/master branch for a newly cloned repo, or the last branch that you worked on in an existing repo.

   > [!TIP]
   > *Working directory* is an operating system concept, whereas *current branch* is a Git concept. Think of the current branch as just an alias for the branch that you are working on, not a new or different branch. Because you can't operate directly on a branch in Git's local repository (that is, the database), the working directory provides a place to add, edit, and delete the current branch's files before you save them back to the local repository. This will become clearer later in the next section, and as you get accustomed to working in Git.

   c. **Git Bash environment**: Before you make any changes to files, it's always a good idea to verify the context of your current Git Bash environment. This context includes the repository and branch that the Git commands will operate on. This is especially important if you have switched to work on other repositories or branches (or possibly a different computer) since the time of creation, or last use, of the repository. In the following Git Bash screenshot, you'll notice that the first line in the command prompt contains the following:

   - The user account that you used to sign in to your computer, and the network name of your computer (`useracct@computer` in green)
   - The location of the current repository's working directory (`/c/Git/Azure-Docs` in yellow, which maps to `c:\Git\Azure-Docs` on a Windows computer)
   - The current branch being worked on (`master` in blue) 

     ![Working branch info](./media/contribute-how-to-write-workflows-major/working-branch.png)

   The previous screenshot also shows the use of the `git branch` command, which is used to list the available branches in the local repository. This is useful if you need to verify the current branch (prefixed with an asterisk), or you need to get the list of available branches before switching to a new one.  

4. Use the `checkout` command to select the branch you want to designate as the current branch:

   ```
   git checkout master
   ```

   The `checkout` command does the following:
   
   - Pulls the master branch files out of the local repository
   - Copies the files into the working directory for editing
   - Selects the master branch as the new current branch (which you can verify in the Git command prompt)

5. Create a new working branch in your local repository. 

   a. If you are not collaborating on a project with others, you can skip this step. If you are collaborating, you will likely need to base the creation of your working branch on an upstream *release branch*. So before you create your working branch, you'll need to make your local repository aware of new release branches in your upstream repository by using the following command:

   ```
   git fetch upstream
   ```

   b. To create your working branch, you again use the `checkout` command, but this time with additional arguments. Run the following command, by substituting the name that you want to use for your new branch in place of `<my-working-branch>`. For `<parent-upstream-branch>`, substitute `master` if you are working independently of others, or the name of a release branch if you are collaborating with others on release work. The `-b` switch will cause Git to first create a local working branch, based on the remote `upstream/<parent-upstream-branch>` branch, and then select it as your new current branch.

   ```
   git checkout -b <my-working-branch> upstream/<parent-upstream-branch>
   ```
   
   >[!NOTE]
   > If you receive an error from the `checkout` command in 5.b., make sure that you completed the `fetch upstream` command in 5.a. first. Also, make sure that you completed all steps in the "Setup" section of this guide, including [Local repository setup](contribute-get-started-setup-local.md). This will ensure that your "upstream" connection to the main repository is established correctly.

6. Establish a copy of your new branch in your fork for future synchronization. The following `git push` command will push/upload a copy of your new `<my-working-branch>` to your fork/origin repository. Be sure to replace `<my-working-branch>` with the same working branch name that you specified in step 5. You use the `-u` switch here and only once in this workflow, which saves additional remote branch tracking information for use by Git:

   ```
   git push -u origin <my-working-branch>
   ```

#### Summary

This section was devoted to work that is usually done only once, at the beginning of the workflow for each contribution project. Each time you start a new project, you will want to review the steps in this section.

Let's summarize what you've completed so far:

- Learned about the Git Bash environment and context, important local repository concepts such as the *working directory* and *current branch*, and how Git uses them to manage work in your local repository  
- Learned how to select a new branch into the working directory, designating it as the current branch
- Created a new working branch in your local repository, based on a branch from your main/upstream repo, and selected it into the working directory as the new current branch
- Pushed a copy of the new working branch up to your fork/origin repo, for future synchronization and use in creating a pull request

In the next section, you will learn about the recurring steps in the workflow. You typically do these multiple times as you iterate and perfect your contributions.

### 2. Make changes to your current branch

Another important concept to understand is a *commit*. Git tracks the set of changes you make, in a unit of work called a commit. A commit is a group of related changes, additions, and deletions applied to one or more of the current branch's files, at a point in time. Commits enable Git to operate on a finite boundary of work, and they give you an easy way to reference the changes later if necessary. 

1. Because updates from other contributors happen frequently and simultaneously during the life of your working branch, it's a good practice to first ensure that their changes are integrated into your branch. This will also help you either prevent merge conflicts, which occur when changes from others cannot be reconciled with your branch, or discover them before they get more complex.

   In the following command, replace `<parent-branch-name>` with the name of the remote branch from which your working branch was created in step 5 of the previous section (for example, `master` or `release-0918`). Note that this command assumes that you still have the desired working branch selected as your current branch.

   ```
   git pull upstream <parent-branch-name>
   ```

   This command downloads the latest changes from the specified upstream branch and merges them into the current branch. If Git can't merge them due to a merge conflict, see [Resolve merge conflicts in Git and GitHub](resolve-merge-conflicts.md) for details on how to resolve it. It's a good practice to get into the habit of doing this regularly throughout each day, to proactively reduce or prevent merge conflicts.

2. Make your file changes. You typically use File Explorer and/or your Markdown editor to create, modify, or delete Markdown files from within your repository's working directory. If you need assistance with Markdown syntax, see [How to use Markdown](contribute-how-to-write-use-markdown.md).

3. As you create, modify, or delete files in your working directory, two things are happening:

   - The operating system is persisting the changes in your working directory to disk, just as it would any file changes.  
   - Git is monitoring the working directory for changes. As you recall, the working directory is where the current branch is being modified. Git does this in order to capture your activity, waiting for you to specify when the pending work should be committed to the local repository.  

   So even though the working directory changes are persisted to your file system, *they are not yet persisted to the local repository's copy of the working branch*. After the changes in your working directory are committed, the state of your working directory and the current branch as it exists in the local repository will be the same.

   You can verify the state of Git's change tracking in the working directory, by using the Git `status` command:  
   
   ```
   git status
   ```
   
   In the following example, you can see in red that the working directory has had two tracked changes (one modified and one deleted file) and an untracked change (a new file). None of them have been committed and persisted to the current branch in the local repository.

   ![Working branch](./media/contribute-how-to-write-workflows-major/status.png)

4. You are likely beginning to notice that persisting changes to your local repo is a multistep process. After the changes are saved in the working directory (file system), there are two additional steps that tell Git to:  

   1. Add the tracked and untracked changes in the working directory to Git's staging area (also known as the index). You can think of the staging area as a holding area that's used to queue the changes that you want to bundle together under the same commit operation. 
   2. Commit everything in the staging area, to the local repository's persistent copy of the working branch.

   You do this by using following pair of commands, respectively. Be sure to use a capital A in the `-A` switch, and replace `<comment>` with a comment to capture a short description of your change.

   ```
   git add -A
   git commit –m "<comment>"
   ```

   After you complete the two-step commit process, the state of the current branch in the local repository will match the state of your working directory. You will find yourself repeating the add/commit steps several times as you add, change, or delete files in your working directory.
   
   If you want to learn more about this process, including details of the staging/committing process, see the [Git Basics - Recording Changes to the Repository](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository) topic in the Pro Git e-book.

5. Finally, use the `git push` command again (but without the `-u` switch), to synchronize your local repo's current branch with the branch of the same name that you uploaded/pushed to your GitHub fork in the previous section:

   ```
   git push origin <my-working-branch>
   ```
  
   You should also do this on a regular basis, to ensure that your changes are backed up on GitHub. If you've already opened a pull request (discussed in a later section), each time you push your branch the pull request will sense the new commits and rerun any automated build/validation steps that are required.

#### Summary

If all your Git Bash commands finished successfully, you should see responses to each, similar to the following screenshot. Notice that the `commit` command successfully committed the three file changes to the local repository, and the `push` command successfully pushed the current branch up to the fork.

![Add, commit, push confirmation](./media/contribute-how-to-write-workflows-major/add-commit-push.png)

This section was devoted to steps of the workflow that tend to be recurring, which you will use several times throughout the life of a working branch. Next we will review the last part of the workflow, which you will do when you believe your contribution is ready to be proposed back into the master branch.

### 3. Open a pull request

When you are ready to submit your content for validation and publishing:

1. Go to your fork's GitHub page, for example, `https://github.com/<GitHub-username>/Azure-Docs`. 

2. Next you will create the pull request. A pull request is a proposal to pull the commits on your working branch, into the parent branch in the main repo from which you created your working branch (in step 5 of the [Create a working branch](#1-create-a-working-branch) section).

   Select the working branch you pushed to your fork (in step 5 of the [Make changes to your current branch](#2-make-changes-to-your-current-branch) section), by using the **Branch:** button on the left. Then select the **New pull request** button to the right to initiate a new pull request:

   ![New pull request button](./media/contribute-how-to-write-workflows-major/new-pull-request-button.png)
   
3. When the **Open a pull request** page appears, verify that your "head" fork and working branch are shown on the right (from), and the docs.microsoft.com "base" repo and parent branch are on the left (to):

   ![Pull request](./media/contribute-how-to-write-workflows-major/open-a-pull-request.png)

   The PR title defaults to a comment that matches the one used in your commit, but you should make sure that it's descriptive. You can also do a quick scan to see if the number of files that you modified matches the number shown on the bottom of the page. If you scroll down, you can also review the exact differences between the branches, so you can verify that the pull request contains the changes/commits that you expect. If the pull request looks correct, select the **Create pull request** button near the bottom.

[!INCLUDE [contribute-how-to-write-workflows-pull-request-processing](../includes/contribute-how-to-write-workflows-pull-request-processing.md)]

## Next steps

That's it! You've made a contribution to docs.microsoft.com content!

- For additional information on the pull request process, see [Pull request best practices](contribute-how-to-write-pull-request-etiquette.md).

- To learn more about topics such as Markdown and Markdown extensions syntax, continue to the "Writing essentials" section.

