# rebase (Simple / Default)
Demo of adding a 'Rebase' main to absorb a new commit on a feature branch

Same setup as the merge example, with Commits A, B + C

The steps:

    git init
    echo 'A' >> A.txt
    git add A.txt
    git commit -m 'A'
    git checkout -b feature

So both branch refs `main` and `feature` both point at commit `A`

Then we add a new commit 'B' onto feature

    echo 'B' >> B.txt
    git add B.txt
    git commit -m 'B'


However, another dev has gotten a PR merged into `main` in the interim:

    git checkout main
    echo 'C' >> C.txt
    git add C.txt
    git commit -m 'C'

So 
- Branch `feature` is 1 commit ahead of `main`
- Branch `main` is 1 commit ahead of 'feature'

![{0ED8C55D-73C6-4D15-BB85-8E5DCD135D2B}](https://github.com/user-attachments/assets/d14cfa69-7c87-46c4-a5e0-c3ba926ee6ac)

> [!NOTE]  
> You'll notice that the commit 'B' on feature has hash a9b1c...

We can no longer fast-forward. One of the options is to rebase:

    git checkout feature
    git rebase main

i.e. 'rewrite the current branch (feature) such that the fork point 'changes' such that all commits in feature since the original forkpoint are now rewritten to instead have the HEAD of 'main' as their new parent

> [!WARNING]  
> It is NOT recommended to rebase main onto feature. You should rebase the short lived branch (feature) onto the trunk (main), as otherwise you will be re-writing main, 
> which may require force push, if the branch is already shared with a remote, and which can cause merge issues with other developers

![{DA5B7B1E-73A4-431D-9D26-5F4513BA241F}](https://github.com/user-attachments/assets/a573312c-96b2-4c27-a162-efba5e32d58f)

You'll note
- Commit B (on feature) has now been 'rewritten', such that the parent commit is now 'C' (the HEAD of main) and is no longer 'A' from where it was branched
- As a result, B has a new commit hash (e05e75eb). Although it has the same content, Author, TimeStamp and commit message as the original commit B, it is technically a new commit (and you'll note that the time causality of the commit history is out of sequence)
- The commit tree is now linear

You'll note that main is now 'lagging behind' feature. It's easy to get main back up to speed by fast forwarding it:

- git checkout main
- git rebase feature --ff

(in reality, when working with a remote, you'll likely instead just push your feature branch up to the remote and issue a pull request, with 'ff only' as the merge option)
The -ff switch is important, as this will prevent any accidental rewriting of the `main' commit history (which could then require force-push nastiness)
