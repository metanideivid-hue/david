mkdir git_exam_george_balaskas
cd git_exam_george_balaskas
git init
git config user.name "George Balaskas"
git config user.email "georgebalaskas@example.com"
git status

echo "# Git Exam Project" > README.md
echo "- Practice basic git workflow" >> README.md
echo "- Work with branches and conflicts" >> README.md
echo "- Push to GitHub repository" >> README.md

git add README.md
git commit -m "Initial commit with README"
git log --oneline

echo "temp.txt" > .gitignore
echo "Temporary file for testing" > temp.txt
git status
git checkout -b feature/notes
echo "Notes line 1" > notes.md
echo "Notes line 2" >> notes.md
echo "Notes line 3" >> notes.md
echo "Notes line 4" >> notes.md
echo "Notes line 5" >> notes.md
git add notes.md
git commit -m "Add study notes file"
git branch
git log --oneline --decorate
git checkout main
nano README.md
- Practice basic git commands and version control
git add README.md
git commit -m "Update README line in main branch"
git checkout feature/notes
nano README.md
- Practice Git workflow step-by-step
git add README.md
git commit -m "Edit README line in feature branch"
git checkout main
git merge feature/notes
git add README.md
git commit -m "Resolve merge conflict and finalize README"
git log --oneline
git remote add origin https://github.com/<username>/git_exam_george_balaskas.git
git remote -v
git push -u origin main
echo "## How to run" >> README.md
echo "Clone the repository and open README.md for instructions." >> README.md
git add README.md
git commit -m "Add How to run section (fix #1)"
git push
