# Make a backup branch for safety
git checkout feature/Marvel_25.9.1-pbi-preview-functionality
git checkout -b backup/Marvel_25.9.1-pbi-preview-functionality

# Switch back to your feature branch
git checkout feature/Marvel_25.9.1-pbi-preview-functionality

# Fetch latest changes from remote
git fetch origin

# Rebase on top of updated feature/Marvel_25.9.1
git rebase origin/feature/Marvel_25.9.1

# If conflicts occur, fix them, then:
# git add <file>
# git rebase --continue

# Push updated branch (after rebase history change)
git push --force-with-lease