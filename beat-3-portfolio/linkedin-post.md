This fall, I built an automated screening tool that vets open-source GitHub issues before I write any code. It reads each issue and checks it against a checklist I built and refined, such as whether someone else is already working on it or whether the problem has already been fixed.

On its first scan, the tool approved an open issue in SQLMesh that appeared to have already been fixed by an earlier merged pull request. I updated the checklist to explicitly fail any open issue with a merged fix or a maintainer statement confirming it was resolved, and sorted results by fewest comments first. The very next day, that new check caught and rejected an unclosed SurfSense issue (#1964) whose fix had already merged two weeks earlier.

The tool is now part of my workflow and helped me vet a valid code-quality issue in the JWST data analysis repository, where I recently opened a fix (PR #2179, open). Building it reinforced a simple habit: verify the state of the problem before writing a single line of code.

https://github.com/smtanaka00/ai301-coursework/blob/main/beat-3-portfolio/technical-writeup.md
