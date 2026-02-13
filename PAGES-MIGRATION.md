# Switch to GitHub Actions for Pages (and remove gh-pages branch)

After you push the updated workflow to `main`, do the following.

## 1. Set Pages source to GitHub Actions

1. Open the repo on GitHub → **Settings** → **Pages**.
2. Under **Build and deployment**:
   - **Source**: choose **GitHub Actions** (not "Deploy from a branch").
3. Save. The site will be served from the workflow’s deployment from now on.

## 2. Remove the gh-pages branch

**On GitHub**

- **Settings** → **Branches** → under "Branch protection" remove any rule that mentions `gh-pages` (if present).
- **Code** → switch branch dropdown → find **gh-pages** → trash icon to delete the branch.

**Locally**

```bash
git fetch --prune
git branch -d gh-pages   # delete local gh-pages (if you're on main)
```

After this, only `main` remains; pushes to `main` will build and deploy via the new workflow.
