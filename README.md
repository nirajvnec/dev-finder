# Make a backup branch (optional but safe)
git checkout feature/Marvel_25.9.1-pbi-preview_functionality
git checkout -b backup/Marvel_25.9.1-pbi-preview_functionality

# Switch back to your feature branch
git checkout feature/Marvel_25.9.1-pbi-preview_functionality

# Fetch latest changes from remote
git fetch origin

# Rebase your branch on top of updated 25.9.1
git rebase origin/25.9.1

# If there are conflicts:
#   1. Fix the conflicts in the files
#   2. Run:
#      git add <file>
#      git rebase --continue
#   Repeat until rebase completes

# Push the rebased branch to GitLab (force-with-lease is safer than --force)
git push --force-with-lease