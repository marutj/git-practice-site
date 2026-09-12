# Git and GitHub Pages practice lab

This repository is a small static website for practising the complete workflow:

1. Work locally with Git.
2. Push the repository to GitHub.
3. Publish it with GitHub Pages.
4. Make and safely undo a bad change.
5. Clone the project onto another computer and continue maintaining it.

## 1. One-time setup

Install Git, create a GitHub account, and set your identity on your computer:

```bash
git --version
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

The email should be one registered with GitHub (or your GitHub no-reply email) if
you want commits linked to your profile.

## 2. Run the website locally

From this repository's directory, run:

```bash
python3 -m http.server 8000
```

Open <http://localhost:8000>. Stop the server with `Ctrl+C`.

## 3. Create the first version

If this directory has not already been initialized and committed, run:

```bash
git init -b main
git add index.html styles.css script.js README.md .gitignore
git commit -m "Create initial practice website"
```

Useful commands to run often:

```bash
git status
git diff
git log --oneline --decorate --graph --all
```

## 4. Put it on GitHub

On GitHub, create a new empty repository named `git-practice-site`. Do **not** add
a README, license, or `.gitignore`, because those files already exist locally.
Then connect and push this local repository (replace `YOUR-USERNAME`):

```bash
git remote add origin https://github.com/YOUR-USERNAME/git-practice-site.git
git push -u origin main
```

GitHub may ask you to sign in. For HTTPS, use a browser/Git Credential Manager or
a personal access token; GitHub account passwords are not accepted by Git over
HTTPS.

## 5. Publish with GitHub Pages

In the GitHub repository:

1. Open **Settings > Pages**.
2. Under **Build and deployment**, choose **Deploy from a branch**.
3. Select branch **main**, folder **/(root)**, then **Save**.
4. Wait for the deployment to finish and revisit the Pages settings for the URL.

For a project repository, the normal URL is:

```text
https://YOUR-USERNAME.github.io/git-practice-site/
```

## 6. Make a normal change on a branch

Branches isolate work until it is reviewed:

```bash
git switch -c change-heading
```

Edit the `<h1>` text in `index.html`, then inspect and save the version:

```bash
git diff
git add index.html
git commit -m "Update home page heading"
git push -u origin change-heading
```

On GitHub, open a pull request from `change-heading` into `main`, review the
change, and merge it. Back on this computer, update and remove the old branch:

```bash
git switch main
git pull --ff-only
git branch -d change-heading
```

## 7. Prove that rollback works

First create a deliberately bad, but harmless, published change. For example,
change the heading to `BROKEN WEBSITE`, then commit and push it:

```bash
git switch main
git pull --ff-only
# Edit index.html now.
git add index.html
git commit -m "Demonstrate a bad heading"
git push
```

Record the newest commit and wait for Pages to show the bad heading:

```bash
git log --oneline -3
```

To undo a commit that has already been shared, use `git revert`. It creates a
new commit that reverses the bad one, preserving an honest, auditable history:

```bash
git revert HEAD
git push
```

After Pages deploys again, the old good heading will be restored. `git log` will
show both the bad commit and the later revert, proving what happened.

### Undo changes that have not been committed

Review first, then restore a file:

```bash
git diff
git restore index.html
```

### Undo the latest local commit that has not been pushed

Keep its file changes so you can fix and recommit them:

```bash
git reset --soft HEAD~1
```

Avoid rewriting shared history with `git reset` plus a force-push. For a pushed
commit, prefer `git revert`.

## 8. Set up a new maintainer's computer

First, add the maintainer as a repository collaborator under **Settings >
Collaborators** (not needed for a public read-only clone). On their computer:

```bash
git --version
git config --global user.name "New Maintainer Name"
git config --global user.email "maintainer@example.com"
git clone https://github.com/YOUR-USERNAME/git-practice-site.git
cd git-practice-site
python3 -m http.server 8000
```

Their routine for each change should be:

```bash
git switch main
git pull --ff-only
git switch -c describe-the-change
# Edit and test files.
git status
git diff
git add <changed-files>
git commit -m "Explain the change"
git push -u origin describe-the-change
```

They then open a pull request on GitHub. Protecting `main` and requiring pull
requests is a useful next step once this basic exercise is comfortable.

## Quick mental model

- **Working directory:** files currently being edited.
- **Staging area:** the exact changes selected by `git add` for the next commit.
- **Local repository:** commits stored on this computer.
- **GitHub remote:** the shared copy reached with `git push` and `git pull`.
- **GitHub Pages:** a website deployment produced from the selected branch.
